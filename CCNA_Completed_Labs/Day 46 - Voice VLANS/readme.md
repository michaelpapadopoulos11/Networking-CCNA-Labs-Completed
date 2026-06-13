# Day 46 - Voice VLANS

## Network:
<img width="679" height="241" alt="d46-network" src="https://github.com/user-attachments/assets/405716bf-7337-4efe-8afe-73aeacbd0286" />

## 1. Configure SW1's interfaces in the appropriate VLANs.
(NOTE: **Telephony configurations aren't relevant to the CCNA and have been pre-configured on R1)**

### SW1 Config:
```
SW1>en
SW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#int range g1/0/2-3
SW1(config-if-range)#switchport mode access
SW1(config-if-range)#switchport access vlan 10
% Access VLAN does not exist. Creating vlan 10
SW1(config-if-range)#
SW1(config-if-range)#switchport voice vlan 20
% Voice VLAN does not exist. Creating vlan 20
SW1(config-if-range)#
```

---

## 2. Configure ROAS for the connection between SW1 and R1.

### SW1 Config:
```
SW1(config-if-range)#int g1/0/1
SW1(config-if)#switchport mode trunk
SW1(config-if)#switchport trunk allowed vlan 10,20
SW1(config-if)#
```

## R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int f0/0
R1(config-if)#no shut

R1(config-if)#
%LINK-5-CHANGED: Interface FastEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0, changed state to up

R1(config-if)#
R1(config-if)#int f0/0.10
R1(config-subif)#
%LINK-3-UPDOWN: Interface FastEthernet0/0.10, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0.10, changed state to up

R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip address 192.168.10.1 255.255.255.0
R1(config-subif)#
R1(config-subif)#int f0/0.20
R1(config-subif)#
%LINK-3-UPDOWN: Interface FastEthernet0/0.20, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface FastEthernet0/0.20, changed state to up

R1(config-subif)#encapsulation dot1q 20
R1(config-subif)#ip address 192.168.20.1 255.255.255.0
R1(config-subif)#
```

---

## 3. In simulation mode, ping PC2 from PC1. Is the traffic tagged with a VLAN ID?

The ping from PC1 to PC2 is **not** tagged with a VLAN ID, if it was a 802.1Q tag would be present in the Ethernet frame.

<div align="center">
  <img src="https://github.com/user-attachments/assets/556b4a3d-fcc8-411a-bb1c-d6d812135790" width="900" />
</div>

As we can see from the image above, no 802.1Q frame is present in the Ethernet frame, meaning the ping isn't tagged with a VLAN ID.

---

## 4. In simulation mode, call PH1 from PH2.  Is the traffic tagged with a VLAN ID?

Based on the image below we can see that PH2 is tagged with a VLAN ID. It contains the TPID:0x 8100 field which refers to 802.1Q encapsulation. Similarly, the TCI:0x 014, "Tagged Control Information" field translates to a VLAN ID of 20, 0014 implies 20 in hexadecimal.

<div align="center">
  <img src="https://github.com/user-attachments/assets/41dabb3c-e17e-4660-a80f-651735476507" width="350" />
</div>
