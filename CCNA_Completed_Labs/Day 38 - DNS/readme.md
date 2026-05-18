# Day 38 - DNS

## Summary:
In this lab I’ll be setting up a small routed network to reach the internet, configuring both the PCs and the router to use DNS for name resolution, adding local host entries on the router so devices can be identified by name, and finally using simulation mode to watch how DNS queries and pings to external domains like youtube.com move through the network.

## Network:

<div align="center">
<img src="https://github.com/user-attachments/assets/89ab1abd-583e-40cb-8af2-68e1363cd597" width="750" />
</div>

## 1. Configure a default route to the Internet on R1.

### R1 Config:
```
R1>en
R1#conf t
R1(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
R1(config)#
R1(config)#do ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
..!!!
Success rate is 60 percent (3/5), round-trip min/avg/max = 0/0/0 ms
```

We configured a default route to the Internet on R1 which works based on the internet ping test shown in the above CLI output.

---

## 2. Configure PC1, PC2, and PC3 to use 1.1.1.1 as their DNS server.

To do this, I simply click on the PC and select: Config ==> DNS Server ==> Set to 1.1.1.1 for PC1, PC2 and PC3.

<div align="center">
<img src="https://github.com/user-attachments/assets/14f5d39c-4cd4-4c62-b168-36a6e7a36f3b" width="350" />
</div>

---

## 3. Configure R1 to use 1.1.1.1 as its DNS server. Configure host entries on R1 for R1, PC1, PC2, and 
PC3. Confirm by pinging PC1 by name from R1.

## R1 Config:
```
R1(config)#ip name-server 1.1.1.1
R1(config)#ip host R1 192.168.0.254
R1(config)#ip host PC1 192.168.0.1
R1(config)#ip host PC2 192.168.0.2
R1(config)#ip host PC3 192.168.0.3
R1(config)#
R1(config)#do show hosts
Default Domain is not set
Name/address lookup uses domain service
Name servers are 1.1.1.1

Codes: UN - unknown, EX - expired, OK - OK, ?? - revalidate
       temp - temporary, perm - permanent
       NA - Not Applicable None - Not defined

Host                      Port  Flags      Age Type   Address(es)
PC1                       None  (perm, OK)  0   IP      192.168.0.1
PC2                       None  (perm, OK)  0   IP      192.168.0.2
PC3                       None  (perm, OK)  0   IP      192.168.0.3
R1                        None  (perm, OK)  0   IP      192.168.0.254
R1(config)#	
R1(config)#do ping PC1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.0.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 0/0/0 ms
```

The above CLI shows us the host table of devices we’ve made for R1, assigning a name with an IPv4 address in the local network. In doing this, we are able to call pings through the name attached to the address over typing in the whole address, `ping PC1` over `ping 192.168.0.1`, though both options still work.

---

## 4. **USE SIMULATION MODE FOR THIS STEP** From PC1, ping youtube.com by name and analyse the messages being sent.

**NOTE : Because R1 had already pinged PC1, PC1 already knows the MAC address of R1s default gateway, meaning PC1 doesn’t need to send an ARP request.**

<div align="center">
<img src="https://github.com/user-attachments/assets/8398bf0f-5036-41c2-beba-bb49b558c505" width="900" />
</div>

**1.** The DNS Query is made from **PC1** which travels to **Server 1 (1.1.1.1)**, then travels back to **PC1**.

The above below captures the **DNS Query** as it’s  returned to **PC1**, the **DNS PDU** that has returned shows the IPv4 address of `172.217.6.78` (YouTube.com address) which **PC1** can now ping.

<div align="center">
<img src="https://github.com/user-attachments/assets/560e7a13-8fb9-4103-b971-e2d5eb3a9e84" width="350" />
</div>

**2.** This shows the initial **ICMP request** that can now occur **(The whole ping wasn’t captured in the screenshot)**.

The image below shows the **PC1 PDU** information, the ICMP ping now has the IP destination of **youtube.com** (`172.217.6.78`), learned through the DNS Query process.

<div align="center">
<img src="https://github.com/user-attachments/assets/0034bd21-0341-4d00-8722-4f0a4b03c511" width="350" />
</div>

## Successful Ping:

<div align="center">
<img src="https://github.com/user-attachments/assets/2e611d99-578e-427a-97dd-94225df50ca0" width="614" height="650" />
</div>
