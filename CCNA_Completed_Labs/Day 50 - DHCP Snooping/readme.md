# Day 49 - DHCP Snooping

## Network:
<img width="508" height="181" alt="d50-netw" src="https://github.com/user-attachments/assets/090de8e8-1e3a-48b1-af07-70a68a2f16ea" />

## 1. Configure R1 as a DHCP server, exclude 192.168.1.1 - 192.168.1.9 from the pool | Default gateway: R1

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.9
R1(config)#
R1(config)#ip dhcp pool POOL1
R1(dhcp-config)#network 192.168.1.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#
```
---

## 2. Configure DHCP snooping on SW1 and SW2. Configure the uplink interfaces as trusted ports.

### SW1 Config:
```
SW1>en
SW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#ip dhcp snooping
SW1(config)#ip dhcp snooping vlan 1
SW1(config)#
SW1(config)#int g0/2
SW1(config-if)#ip dhcp snooping trust
SW1(config-if)#
```

**Based on the SW1 config, G0/2 is the trusted uplink because it’s pointing away from the end hosts and towards the router, (important for security reasons…..)**

### SW2 Config:
```
SW2>en
SW2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#ip dhcp snooping
SW2(config)#ip dhcp snooping vlan 1
SW2(config)#
SW2(config)#int g0/1
SW2(config-if)#ip dhcp snooping trust
SW2(config-if)#exit
SW2(config)#
```

---

## 3. Use IPCONFIG /RENEW on PC1 to get an IP address. Does it work? Why or why not?

The `ipconfig /renew` command fails on PC1, based on the Packet Tracer simulation mode, we can see where the failure occurs.

<p align="center">
  <img src="https://github.com/user-attachments/assets/73e1a9bf-c2e5-4d0a-a54f-09954866fd2a"
       alt="d50-dhcp-frame"
       width="350">
</p>

The image above depicts the data that’s come in from **PC1 ==> SW2 ==> SW1.** As has been highlighted, the DHCP Agent Information Option has been added when the data was forwarded from **PC1 ==> SW2.**

SW1 received the data on an untrusted port and decided to discard it.

---

## 4. If it doesn't work, make the necessary configuration change to fix it.

I added the `no ip dhcp snooping information option` command on SW1 and SW2, they stop inserting Option 82. With no extra DHCP metadata added, SW1 no longer treats the packet as untrusted, so 
the DHCP messages pass through cleanly and PC1 is finally able to get an address.
