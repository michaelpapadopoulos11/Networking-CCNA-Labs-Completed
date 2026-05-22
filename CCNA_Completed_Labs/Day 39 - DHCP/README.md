## Day 39 - DHCP

In this section of the course, we have been learning about DHCP. In this lab we will be configuring R2 as a **DHCP server** and R1 as a **DHCP Client/Relay Agent** to distribute IP addresses to the two PCs in the network.

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/45aa4aa8-4ce5-432f-9471-765100f58b29" width="80%">
</p>

## 1. Configure the following DHCP pools on R2:

### POOL1 — 192.168.1.0/24  
- Reserved: 192.168.1.1 - 192.168.1.10  
- DNS: 8.8.8.8  
- Domain: jeremysitlab.com  
- Default Gateway: R1  

### POOL2 — 192.168.2.0/24  
- Reserved: 192.168.2.1 - 192.168.2.10  
- DNS: 8.8.8.8  
- Domain: jeremysitlab.com  
- Default Gateway: R2  

### POOL3 — 203.0.113.0/30  
- Reserved: 203.0.113.1  

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.10
R2(config)#ip dhcp excluded-address 192.168.2.1 192.168.2.10
R2(config)#ip dhcp excluded-address 203.0.113.1
R2(config)#
R2(config)#ip dhcp pool POOL1
R2(dhcp-config)#network 192.168.1.0 255.255.255.0
R2(dhcp-config)#dns-server 8.8.8.8
R2(dhcp-config)#domain-name jeremysitlab.com
R2(dhcp-config)#default-router 192.168.1.1
R2(dhcp-config)#
R2(dhcp-config)#ip dhcp pool POOL2
R2(dhcp-config)#network 192.168.2.0 255.255.255.0
R2(dhcp-config)#dns-server 8.8.8.8
R2(dhcp-config)#domain-name jeremysitlab.com
R2(dhcp-config)#default-router 192.168.2.1
R2(dhcp-config)#
R2(dhcp-config)#ip dhcp pool POOL3
R2(dhcp-config)#network 203.0.113.0 255.255.255.252
R2(dhcp-config)#
```

### R2 Output:
```
R2(dhcp-config)#do sh run | section dhcp
ip dhcp excluded-address 192.168.1.1 192.168.1.10
ip dhcp excluded-address 192.168.2.1 192.168.2.10
ip dhcp excluded-address 203.0.113.1
ip dhcp pool POOL1
 network 192.168.1.0 255.255.255.0
 default-router 192.168.1.1
 dns-server 8.8.8.8
 domain-name jeremysitlab.com
ip dhcp pool POOL2
 network 192.168.2.0 255.255.255.0
 default-router 192.168.2.1
 dns-server 8.8.8.8
 domain-name jeremysitlab.com
ip dhcp pool POOL3
 network 203.0.113.0 255.255.255.252
R2(dhcp-config)#
```

Based on the above configuration, I’ve setup  DHCP on R2, the **DHCP Server**, where I excluded the IPs I didn’t want to assign **(192.168.1.1–192.168.1.10 / 192.168.2.1–192.168.2.10 / 203.0.113.1)** and I created three DHCP pools so the router can give out addresses, gateways, DNS, and the domain name to devices on the 192.168.1.0, 192.168.2.0, and 203.0.113.0 subnets.

<p align="center">
  <img src="https://github.com/user-attachments/assets/585fac94-55e1-4935-a1d0-b27670acb468" width="60%">
</p>

Based on the image above, PC2 previously wasn’t assigned any information and after using `ipconfig /renew`, it now assigns an IP Address, DNS Server and Default Gateway after setting up  DHCP on R2.

---

## 2. Configure R1's G0/0 interface as a DHCP client. What IP address did it configure?


### R1 Output:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int g0/0
R1(config-if)#ip address dhcp
R1(config-if)#no shut

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up

R1(config-if)#
%DHCP-6-ADDRESS_ASSIGN: Interface GigabitEthernet0/0 assigned DHCP address 203.0.113.2, mask 255.255.255.252, hostname R1


R1(config-if)#
```
Based on the above output, R1 has been assigned the IP address **203.0.113.2/30**.

<img width="2000" height="1005" alt="d39-step-2-process" src="https://github.com/user-attachments/assets/130ac397-455d-4806-8266-ca4a387b53fc" />

In order for R1 to become a **DHCP Client**, the image above demonstrates the process that needs to occur for this to happen.

---

## 3. Configure R1 as a DHCP relay agent for the 192.168.1.0/24 subnet.

### R1 Config:
```
R1(config-if)#int g0/1
R1(config-if)#ip helper-address 203.0.113.1
R1(config-if)#
```

**R1 is now a relay agent.**
 
---

## 4. Use the CLI of PC1 and PC2 to make them request an IP address from their DHCP server.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9f7ee57d-949d-4477-b755-3dacea658d2a" width="45%">
  <img src="https://github.com/user-attachments/assets/200583ba-5248-490b-9dd3-b954a2a1e629" width="45%">
</p>

The above screenshots show that DHCP was successfully configured to work on both PCs in the network!
