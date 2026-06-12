# Day 45 - Dynamic NAT

## Network:
<img width="627" height="216" alt="d45-netw" src="https://github.com/user-attachments/assets/cd76c4a8-30a7-4901-a292-cef75bfa9e8b" />

## 1. Configure dynamic NAT on R1.
   **> Configure the appropriate inside/outside interfaces**
   
   **> Translate all traffic from 172.16.0.0/24**
   
   **> Create a pool of 100.0.0.1 to 100.0.0.2 from the 100.0.0.0/24 subnet**

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g0/0
R1(config-if)#ip nat outside
R1(config-if)#
R1(config-if)#int g0/1
R1(config-if)#ip nat inside
R1(config-if)#exit
R1(config)#
R1(config)#access-list 1 permit 172.16.0.0 0.0.0.255
R1(config)#ip nat pool POOL1 100.0.0.1 100.0.0.2 netmask 255.255.255.0
R1(config)#ip nat inside source list 1 pool POOL1
```

We defined our boundaries by marking **g0/1 as the internal network (inside)** and **g0/0 as the 
internet-facing side (outside)**. We then created an Access Control List (ACL 1) to identify 
our internal **PCs (172.16.0.0/24)** and mapped them to a Dynamic NAT pool containing 
only two public IP addresses **(100.0.0.1 and 100.0.0.2)**.

---

## 2. Ping google.com from PC1 and PC2.  Then, ping it from PC3. What happens to PC3's ping?

**PC1 ==> Google.com** Ping is successful!

**PC2 ==> Google.com** Ping is successful!

**PC3 ==> Google.com** no response, this is because in the NAT POOL we previously configured, 
the two addresses are being used by PC1 and PC2, meaning PC3 doesn’t have a address it can 
be assigned and the ping is therefore dropped.

---

## 3. Clear the NAT translations and remove the current NAT configuration. Switch the configuration to PAT using R1's public IP address.

To clear the current NAT translations we use `R1(config)#do clear ip nat translation *`.

To switch the configuration to PAT we use:
```
R1(config)#ip nat inside source list 1 interface g0/0 overload
R1(config)#do sh run | include nat
 ip nat outside
 ip nat inside
ip nat pool POOL1 100.0.0.1 100.0.0.2 netmask 255.255.255.0
ip nat inside source list 1 interface GigabitEthernet0/0 overload
R1(config)#
```

---

## 4. Ping google.com from each PC.  Do the pings work? Examine the NAT translations on R1.

The ping is successful from each PC end host.

### R1 NAT Table Output:
```
R1(config)#do sh ip nat translations
Pro  Inside global     Inside local       Outside local      Outside global
icmp 203.0.113.1:1024  172.16.0.1:5       172.217.175.238:5  172.217.175.238:1024
icmp 203.0.113.1:1025  172.16.0.1:6       172.217.175.238:6  172.217.175.238:1025
icmp 203.0.113.1:1026  172.16.0.1:7       172.217.175.238:7  172.217.175.238:1026
icmp 203.0.113.1:1027  172.16.0.1:8       172.217.175.238:8  172.217.175.238:1027
icmp 203.0.113.1:1     172.16.0.3:1       172.217.175.238:1  172.217.175.238:1
icmp 203.0.113.1:2     172.16.0.3:2       172.217.175.238:2  172.217.175.238:2
icmp 203.0.113.1:3     172.16.0.3:3       172.217.175.238:3  172.217.175.238:3
icmp 203.0.113.1:4     172.16.0.3:4       172.217.175.238:4  172.217.175.238:4
icmp 203.0.113.1:5     172.16.0.2:5       172.217.175.238:5  172.217.175.238:5
icmp 203.0.113.1:6     172.16.0.2:6       172.217.175.238:6  172.217.175.238:6
icmp 203.0.113.1:7     172.16.0.2:7       172.217.175.238:7  172.217.175.238:7
icmp 203.0.113.1:8     172.16.0.2:8       172.217.175.238:8  172.217.175.238:8
udp 203.0.113.1:1024   172.16.0.2:1026    8.8.8.8:53         8.8.8.8:53
udp 203.0.113.1:1025   172.16.0.1:1026    8.8.8.8:53         8.8.8.8:53
udp 203.0.113.1:1026   172.16.0.3:1026    8.8.8.8:53         8.8.8.8:53

R1(config)#
```

By adding the `overload` keyword, we changed **Dynamic NAT into PAT (Port Address Translation)**. 
Instead of assigning a unique public IP to every individual device, all three PCs now share 
a single public interface IP (203.0.113.1). **The router uses unique Port Numbers 
(like :1024, :1025)** to keep track of whose traffic is whose, preventing our address pool 
from running dry.
