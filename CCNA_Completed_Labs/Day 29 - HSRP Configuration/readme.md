## Day 29 - Configuring HSRP

### Network:

<p align="center">
  <img src="https://github.com/user-attachments/assets/68222475-7c6c-45a0-bd61-86d4af38e680" width="650">
</p>

---

## 1. Ping external server 8.8.8.8 from PC1 and PC2 | What is the default gateway configured as?

PC1 and PC2 both successfully ping the server 8.8.8.8.

<p align="center">
  <img src="https://github.com/user-attachments/assets/deb2a78a-2be9-4e5c-9c47-81110e497989" width="350">
</p>

The default gateway on both PCs are set to 10.0.1.253, R1s address.

---

## 2. Configure HSRPv2 on R1/R2 | Raise R1's priority above the default, lower R2's priority below the default | Enable preemption.

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g0/0
R1(config-if)#standby ver 2
R1(config-if)#standby 1 ip 10.0.1.254
R1(config-if)#
%HSRP-6-STATECHANGE: GigabitEthernet0/0 Grp 1 state Init -> Init

R1(config-if)#standby 1 priority 200
R1(config-if)#standby 1 preempt
R1(config-if)#
```

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#int g0/0
R2(config-if)#standby ver 2
R2(config-if)#standby 1 ip 10.0.1.254
R2(config-if)#
%HSRP-6-STATECHANGE: GigabitEthernet0/0 Grp 1 state Init -> Init

R2(config-if)#standby 1 priority 50
%HSRP-6-STATECHANGE: GigabitEthernet0/0 Grp 1 state Speak -> Standby
R2(config-if)#standby 1 preempt
```

---

## 3. Configure the VIP as the default gateway of PC1/PC2 | Ping 8.8.8.8 from the PCs.  Check the PCs' ARP table.

From here I clicked on PC1 and PC2 and chose: Desktop --> IP Configuration --> Set the Default Gateway to 10.0.1.254.

<p align="center">
  <img src="https://github.com/user-attachments/assets/af967b83-fea1-45e2-8631-adca9cff0c3c" width="350">
</p>

The above image shows the successful ping from PC1 to 8.8.8.8, as well as displaying the arp table using the `arp -a` command.

### What MAC address is mapped to the VIP?

The `0000.0c9f.f001` is mapped to the VIP address.

---

## 4. Turn off R1 (save the config first!). After it restarts, ping from PC1 to 8.8.8.8 again. Is R2 used as the default gateway?

I used the `write` command to save the config on R1 and shut the router off through the "Physical" tab.

<img width="1271" height="416" alt="d29-step4" src="https://github.com/user-attachments/assets/77a109f1-b8c3-4ed9-8795-a2246369fc4e" />

The above image shows the ping going through in spite of the interface links from R1 being down. R2 has taken over as the active router in the HSRP process.

---

## 5. Turn on R1 again. Does it become the active router again?

I turned R1 back on through the "Physical" tab and sped up the booting process.

With the R1 interfaces active again, I pinged from PC1 to 8.8.8.8 and checked the `tracert 8.8.8.8` command. Sure enough, R1 is the active router again.

<img width="1271" height="502" alt="d29-step5" src="https://github.com/user-attachments/assets/592352e9-e61c-4e37-9941-4904f2cebaf5" />

