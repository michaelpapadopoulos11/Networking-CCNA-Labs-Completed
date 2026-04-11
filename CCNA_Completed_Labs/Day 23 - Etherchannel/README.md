# Day 23 — EtherChannel Configuration

This lab configures multiple EtherChannels (Layer 2 and Layer 3), verifies load‑balancing behavior, and ensures end‑to‑end connectivity to SRV1.

## 1. Configure Layer 2 EtherChannel between ASW1 and DSW1 using LACP - Configure it as a trunk.

After checking the `sh spanning tree` on ASW1, we can see that G0/1 is a root port and G0/2 is an alternate port, meaning G0/2 won't forward or recieve any frames and is acting as a backup.

### ASW1 Config:
```
ASW1>en	
ASW1#conf t
ASW1(config)#int range g0/1 - 2
ASW1(config-if-range)#channel-group 1 mode active

ASW1(config-if-range)#int po1
ASW1(config-if)#switchport mode trunk
ASW1(config-if)#
```

After running `do sh run` we can see the G0/1 and G0/2 are correctly configured:

<p align="center">
  <img src="https://github.com/user-attachments/assets/fe9e6386-ed54-4393-bb4e-baba3a9bcc23" width="350">
</p>

The configuration of DSW1 will be the same as on ASW1.

### DSW1 Config:
```
DSW1>en
DSW1#conf t
DSW1(config)#int range g1/0/3 - 4
DSW1(config-if-range)#channel-group 1 mode 

DSW1(config-if-range)#int po1
DSW1(config-if)#switchport mode trunk
DSW1(config-if)#
```

After checking `do sh eth sum` we can see that Po1 is marked as "SU", meaning it's Layer 2 and "in use", which is good!

--- 

## 2. Configure Layer 2 EtherChannel between ASW2 and DSW2 using PAgP - Configure it as a trunk.

This config will be very similar to step 1 with the exception of the etherchannel mode, from LACP to PAgP now.

### ASW2 Config:
```
ASW2>en
ASW2#conf t
ASW2(config)#int range g0/1 - 2
ASW2(config-if-range)#channel-group 1 mode desirable

ASW2(config-if-range)#int po1
ASW2(config-if)#switchport mode trunk
ASW2(config-if)#
```

### DSW2 Config:
```
DSW2>en
DSW2#conf t
DSW2(config)#int range g1/0/3 - 4
DSW2(config-if-range)#channel-group 1 mode desirable

DSW2(config-if-range)#
DSW2(config-if-range)#int po1
DSW2(config-if)#switchport mode trunk
DSW2(config-if)#
```

After viewing the `do sh eth sum` we can see that Po1 is marked as SU which is another good sign.

---

## 3. Configure Layer 3 EtherChannel between DSW1 and DSW2 using static EtherChannel.

### DSW1 Config:
```
DSW1(config-if)#int range g1/0/1 - 2
DSW1(config-if-range)#no switchport

DSW1(config-if-range)#channel-group 2 mode on
DSW1(config-if-range)#int po2
DSW1(config-if)#ip address 10.0.0.1 255.255.255.252
DSW1(config-if)#
```

### DSW2 Config:
```
DSW2(config-if)#in range g1/0/1 - 2
DSW2(config-if-range)#no switchport

DSW2(config-if-range)#channel-group 2 mode on
DSW2(config-if-range)#int po2
DSW2(config-if)#ip address 10.0.0.2 255.255.255.252
DSW2(config-if)#
```

After viewing `do sh eth sum` on both DSW1 and DSW2 we can see that both Po2 are marked "RU", indicating it's Layer 3 and "in use" which is good!

---

## 4. Configure routes to allow the PCs to reach SRV1.

We need to enable routing on DSW1 as well as configure a static route for it to reach SRV1's subnet.

### DSW1 Config:
```
DSW1(config)#ip routing
DSW1(config)#ip route 172.16.2.0 255.255.255.0 10.0.0.2
DSW1(config)#
```

Same config with DSW2, though this one needs to be routed to the 172.16.1.0 network.

### DSW2 Config:
```
DSW2(config)#ip routing
DSW2(config)#ip route 172.16.1.0 255.255.255.0 10.0.0.1
DSW2(config)#
```

The image below shows the ping from PC1 to SRV1, proving the ip routing configs worked!

<p align="center">
  <img src="https://github.com/user-attachments/assets/d4603a8e-9807-439c-98d5-623323041f07" width="350">
</p>

---

## 5. What is the default EtherChannel load-balancing method used on each switch?

### ASW1 & ASW2:
```
ASW1(config)#do sh etherchannel load-balance
EtherChannel Load-Balancing Operational State (src-mac):
Non-IP: Source MAC address
  IPv4: Source MAC address
  IPv6: Source MAC address

ASW1(config)#
```

ASW1 and ASW2 uses the "src-mac" address to load balance.

### DSW1 & DSW2:
```
DSW2(config)#do sh eth load-balance
EtherChannel Load-Balancing Configuration:
        src-mac

EtherChannel Load-Balancing Addresses Used Per-Protocol:
Non-IP: Source MAC address
  IPv4: Source MAC address
  IPv6: Source MAC address

DSW2(config)#
```

DSW1 and DSW2 also use the "src-mac" address as their load-balancing methods.

---

## 6. Configure the switches to load-balance based on source and destination IP addresses.

### ASW1 Config:
```
ASW1(config)#port-channel load-balance src-dst-ip
ASW1(config)#
ASW1(config)#do sh etherchannel load-balance
EtherChannel Load-Balancing Operational State (src-dst-ip):
Non-IP: Source XOR Destination MAC address
  IPv4: Source XOR Destination IP address
  IPv6: Source XOR Destination IP address

ASW1(config)#
```
#### Repeat this for ASW2, DSW1 and DSW2 switches in the lab.

---

## Final Topology:
![d23-final-topology](https://github.com/user-attachments/assets/8ac930e1-b8c5-432b-96b4-355e36f1031e)
