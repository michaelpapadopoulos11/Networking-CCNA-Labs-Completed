# Day 44 - Static NAT

## Network:
<img width="628" height="223" alt="d44-netw" src="https://github.com/user-attachments/assets/c81645ab-bdce-4143-87df-02f887952ec9" />

## 1. Attempt to ping from PC1 to 8.8.8.8.  Does the ping work?

The ping from **PC1** **does not work** to the **Server** (8.8.8.8). This is because **Static NAT** has not yet been configured on R1.

---

## 2. Configure static NAT on R1.
   ### ==> Configure the appropriate inside/outside interfaces.**
   ### ==> Map the IP addresses of PC1, PC2, and PC3 to 100.0.0.x/24.**

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g0/0
R1(config-if)#ip nat outside
R1(config-if)#int g0/1
R1(config-if)#ip nat inside
R1(config-if)#
R1(config-if)#exit
R1(config)#
R1(config)#ip nat inside source static 172.16.0.1 100.0.0.1
R1(config)#ip nat inside source static 172.16.0.2 100.0.0.2
R1(config)#ip nat inside source static 172.16.0.3 100.0.0.3
R1(config)#exit
R1#
%SYS-5-CONFIG_I: Configured from console by console

R1#
R1#show ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
---  100.0.0.1         172.16.0.1         ---                ---
---  100.0.0.2         172.16.0.2         ---                ---
---  100.0.0.3         172.16.0.3         ---                ---

R1#show ip nat statistics
Total translations: 3 (3 static, 0 dynamic, 0 extended)
Outside Interfaces: GigabitEthernet0/0
Inside Interfaces: GigabitEthernet0/1
Hits: 0  Misses: 0
Expired translations: 0
Dynamic mappings:
R1#
```

In the above config, static NAT was configured on R1 by marking **g0/1 as the inside interface**, **g0/0 as the outside interface**, and **creating three one‑to‑one static translations** from the **internal local** PC hosts (172.16.0.X/24) to their matching 100.0.0.x public addresses.

---

## 3. Ping 8.8.8.8 from PC1 again.  Does the ping work?

The ping from **PC1** to the **Server** is now successful because of the **Static NAT** configuration we made.
<p align="center">
  <img src="https://github.com/user-attachments/assets/95c084c9-131d-4deb-853b-2f250af85830" width="350">
</p>

---

## 4. Ping google.com from each PC, and then check the NAT translations on R1.

The ‘google.com’ ping worked for every **PC** in the network, shown in the image below:
<p align="center">
  <img src="https://github.com/user-attachments/assets/e273ced9-c1cf-45e9-9b17-49f568aefd79" 
       alt="d44-step4" 
       width="350">
</p>

### R1:
```
R1#sh ip nat trans
Pro  Inside global     Inside local       Outside local      Outside global
icmp 100.0.0.2:1       172.16.0.2:1       172.217.175.238:1  172.217.175.238:1
icmp 100.0.0.2:2       172.16.0.2:2       172.217.175.238:2  172.217.175.238:2
icmp 100.0.0.2:3       172.16.0.2:3       172.217.175.238:3  172.217.175.238:3
icmp 100.0.0.2:4       172.16.0.2:4       172.217.175.238:4  172.217.175.238:4
icmp 100.0.0.3:1       172.16.0.3:1       172.217.175.238:1  172.217.175.238:1
icmp 100.0.0.3:2       172.16.0.3:2       172.217.175.238:2  172.217.175.238:2
icmp 100.0.0.3:3       172.16.0.3:3       172.217.175.238:3  172.217.175.238:3
icmp 100.0.0.3:4       172.16.0.3:4       172.217.175.238:4  172.217.175.238:4
---  100.0.0.1         172.16.0.1         ---                ---
---  100.0.0.2         172.16.0.2         ---                ---
---  100.0.0.3         172.16.0.3         ---                ---
udp 100.0.0.1:1025     172.16.0.1:1025    8.8.8.8:53         8.8.8.8:53
udp 100.0.0.2:1025     172.16.0.2:1025    8.8.8.8:53         8.8.8.8:53
udp 100.0.0.3:1025     172.16.0.3:1025    8.8.8.8:53         8.8.8.8:53

R1#
```

Based on the **NAT table**, we can see the **UDP** entries at the bottom showing traffic to **8.8.8.8** on **port 53**, which is the **DNS port**. These entries were created when each **PC** pinged ‘google.com’ and needed DNS to resolve its IP address.

Additionally, the **ICMP** entries above represent the ping traffic from our **local PCs** (shown by the **Inside Local** addresses) to **Google’s IP address 172.217.175.238**, which appears as the **Outside Global** address.

---

## 5. Clear the NAT translations on R1.  Which entries remain?

### R1 Config:
```
R1#clear ip nat translation *
R1#show ip nat trans
Pro  Inside global     Inside local       Outside local      Outside global
---  100.0.0.1         172.16.0.1         ---                ---
---  100.0.0.2         172.16.0.2         ---                ---
---  100.0.0.3         172.16.0.3         ---                ---

R1#
```
We use `clear ip nat translation *` to clear the **NAT Table**. After viewing the **NAT Table** after it has been cleared we can see that **only the static entries remain.**
