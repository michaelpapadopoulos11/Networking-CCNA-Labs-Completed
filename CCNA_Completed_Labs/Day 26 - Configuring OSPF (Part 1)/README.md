# Day 26 - Configuring OSPF (Part 1)

### Network:

<p align="center">
  <img src="https://github.com/user-attachments/assets/f456f839-5be8-4925-8bb2-90d014daace2" width="750" />
</p>

---

## 1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces. (You don't have to configure ISPR1)

### R1 Config:
```
Router>en
Router#conf t
Router(config)#hostname R1
R1(config)#int g0/0
R1(config-if)#ip address 10.0.12.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#

R1(config-if)#int f1/0
R1(config-if)#ip address 10.0.13.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#
```

Repeat this process for R2, R3 and R4.

---

## 2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)


### R1 Config:
```
R1(config)#int l0
%LINK-3-UPDOWN: Interface Loopback0, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback0, changed state to up

R1(config-if)#ip address 1.1.1.1 255.255.255.255
R1(config-if)#do sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     10.0.12.1       YES manual up                    up 
FastEthernet1/0        unassigned      YES unset  up                    up 
FastEthernet2/0        unassigned      YES unset  administratively down down 
GigabitEthernet3/0     unassigned      YES unset  administratively down down 
Loopback0              1.1.1.1         YES manual up                    up
R1(config-if)#
```

The above CLI shows that R1's loopback interface was successfully made using `do show ip interface brief` command.

This process was repeated for R2, R3 and R4.

---

## 3. Configure OSPF on each router. Enable OSPF on each interface (including loopback interfaces). (Do not enable OSPF on R1's Internet link) Configure passive-interfaces where appropriate (including loopback interfaces).

### R1 Config:
```
R1(config)#router ospf 1
R1(config-router)#network 10.0.12.0 0.0.0.3 area 0
R1(config-router)#network 10.0.13.0 0.0.0.3 area 0	
R1(config-router)#network 1.1.1.1 0.0.0.0 area 0
R1(config-router)#passive-interface g3/0
R1(config-router)#passive-interface l0
R1(config-router)#
```

In the above CLI we've successfully enabled and configured OSPF on R1.

The neighbouring networks have been connected to R1 as well as R1's loopback address (1.1.1.1/32).

We've setup the passive-interface for G3/0 because it isn't connected to a router, as well as R1s loopback address because loopbacks don't form adjacencies, they're only advertised.

**R2, R3 and R4 configurations were very similar to R1's so I won't show them.**

---

## 4. Configure R1 as an ASBR that advertises a default route in to the OSPF domain.

### R1 Config:
```
R1(config)#int g3/0
R1(config-if)#ip address 203.0.113.1 255.255.255.252
R1(config-if)#no shut
R1(config-if)#

R1(config)#router ospf 1
R1(config-router)#default-information originate
R1(config-router)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
R1(config)#
```

We first need to assign R1's G3/0 port to `203.0.113.1/30` and then we can assign the default gateway to the router.

---

### 5. Check the routing tables of R2, R3, and R4. What default route(s) were added?

By using the `do sh ip route` command we can see the default routes assigned on R2, R3 and R4:

<p align="center">
  <img src="https://github.com/user-attachments/assets/cca5000c-6fc3-4e8e-9ca3-70fbd06f7663" width="260" />
  <img src="https://github.com/user-attachments/assets/28c6acab-1859-4be1-bab7-4e1343c5887b" width="260" />
  <img src="https://github.com/user-attachments/assets/a4f65135-163d-4283-8ea3-bd2f20c363c0" width="260" />
</p>

**Each of the default route has been highlighted in yellow.**

Note that R4 selects both R2 and R3 paths as it's default gateway, meaning it will load balance over the two different networks.

---

### Final Topology:
<img width="1573" height="1028" alt="day-26-final-top" src="https://github.com/user-attachments/assets/afa17b96-3930-4a48-a397-989a8a59f117" />

