# Day 25 - Configuring EIGRP

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/938e77b4-59bb-4371-8c84-04e174d8e392" width="650">
</p>

## 1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces.

### R1 Config:
```
Router>en
Router#conf t
Router(config)#hostname R1
R1(config)#int f1/0
R1(config-if)#ip address 10.0.13.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#

R1(config-if)#int g0/0
R1(config-if)#ip address 10.0.12.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#
```

I haven't shown the other router configs because they're essentially the same as this one, just different hostnames, interfaces, IP address and subnet masks.

---

## 2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)

### R1 Config:
```
R1#sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     10.0.12.1       YES manual up                    up 
FastEthernet1/0        10.0.13.1       YES manual up                    up 
FastEthernet2/0        unassigned      YES unset  administratively down down

R1#conf t
R1(config)#interface loopback 0

R1(config-if)#
%LINK-3-UPDOWN: Interface Loopback0, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback0, changed state to up

R1(config-if)#ip address 1.1.1.1 255.255.255.255
R1(config-if)#
R1(config-if)#do sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     10.0.12.1       YES manual up                    up 
FastEthernet1/0        10.0.13.1       YES manual up                    up 
FastEthernet2/0        unassigned      YES unset  administratively down down 
Loopback0              1.1.1.1         YES manual up                    up
R1(config-if)#
```
This shows CLI section shows the `interface brief` before and after the addition of the Loopback interface being added to R1. I repeated this process on R2, R3 and R4, just changing the address for each router.

---

### 3. Configure EIGRP on each router | Disable auto-summary | Enable EIGRP on each interface (including loopback interfaces) | Configure passive interfaces where appropriate (including loopback interfaces).

### R1 Config:
```
1) R1(config-if)#router eigrp 100
2) R1(config-router)#netw 10.0.12.0  0.0.0.3
3) R1(config-router)#netw 10.0.13.0  0.0.0.3
4) R1(config-router)#no auto-summary
5) R1(config-router)#
6) R1(config-router)#passive-interface g0/0
7) R1(config-router)#passive-interface l0
8) R1(config-router)#
```

- (Line 1) - The `100` indicates the Autonomous System (AS) number. All routers in this network must use this AS value to become EIGRP neighbors.
- (Line 2 - 3) - These lines tell R1 to run EIGRP on the 10.0.12.0/30 and 10.0.13.0/30 links. **Note: 0.0.0.3 implies /30 however EIGRP uses wildcard notation instead of a traditional subnet mask.**
- (Line 4) - Disables classful routing (Class A / Class B / Class C) and makes it so EIGRP advertises the exact subnet instead of rounding it to a classful boundary.
- (Line 6) - This stops EIGRP from forming neighbors on that interface but still advertises the network.

### R3 Config:
```
Router(config-if)#
Router(config-if)#router eigrp 100
Router(config-router)#network 10.0.13.0 0.0.0.3
Router(config-router)#network 10.0.34.0 0.0.0.3
Router(config-router)#network 3.3.3.3 0.0.0.0
Router(config-router)#
Router(config-router)#no auto-summary
Router(config-router)#passive-interface l0
Router(config-router)#
Router(config-router)#do sh ip protocols

Routing Protocol is "eigrp  100 " 
  Outgoing update filter list for all interfaces is not set 
  Incoming update filter list for all interfaces is not set 
  Default networks flagged in outgoing updates  
  Default networks accepted from incoming updates 
  EIGRP metric weight K1=1, K2=0, K3=1, K4=0, K5=0
  EIGRP maximum hopcount 100
  EIGRP maximum metric variance 1
Redistributing: eigrp 100
  Automatic network summarization is not in effect  
  Maximum path: 4
  Routing for Networks:  
     10.0.13.0/30
     10.0.34.0/30
     3.3.3.3/32
  Passive Interface(s): 
    Loopback0
  Routing Information Sources:  
    Gateway         Distance      Last Update 
    10.0.13.1       90            1212778    
  Distance: internal 90 external 170

Router(config-router)#
```

(R2 and R4 were configured in the same way as R1 and R3 but for their neighbouring pairs)

---

## 4. Configure R1 to perform unequal-cost load-balancing when sending network traffic to 192.168.4.0/24

### R1 Config:
```
R1(config)#route eigrp 100
R1(config-router)#variance 2
R1(config-router)#
```

---

## Final Topology:
<p align="center">
  <img src="https://github.com/user-attachments/assets/0e6e3c6d-6d7c-4201-acd4-e59d3bb27cbe" width="650">
</p>
