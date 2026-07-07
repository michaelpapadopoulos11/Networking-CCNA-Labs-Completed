# Day 11 (Part 1) – Configuring Static Routes

## Network Topology:
<div align="center">
  <img src="https://github.com/user-attachments/assets/627fd85a-4dec-4f7f-b724-07a062491440" width="600">
</div>

In this lab I will be configuring static routes on the routers so PC1 and PC2 can successfully ping each other. The topology uses three routers (R1, R2 and R3), each with interfaces in different subnets. After assigning IP addresses to all router interfaces, we add the required static routes:

- R1 needs one route to R3’s 192.168.3.0/24 network.
- R2 needs two routes to R1’s 192.168.1.0/24 and R3’s 192.168.3.0/24 network.
- R3 needs one route to R1’s 192.168.1.0/24 network.

### R1 Config:
```
Router>en
Router#conf t
Router(config)#hostname R1
R1(config)#
R1(config)#int g0/1
R1(config-if)#ip address 192.168.1.254 255.255.255.0
R1(config-if)#no shut
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/1, changed state to up
%LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/1, changed state to up
R1(config-if)#int g0/0
R1(config-if)#ip address 192.168.12.1 255.255.255.0
R1(config-if)#no shut
R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up
R1(config-if)#ex
R1(config-if)#
R1(config)#do sh ip route

Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
* - candidate default, U - per-user static route, o - ODR
P - periodic downloaded static route
Gateway of last resort is not set
192.168.1.0/24 is variably subnetted, 2 subnets, 2 masks
C 192.168.1.0/24 is directly connected, GigabitEthernet0/1
L 192.168.1.254/32 is directly connected, GigabitEthernet0/1
192.168.12.0/24 is variably subnetted, 2 subnets, 2 masks
C 192.168.12.0/24 is directly connected, GigabitEthernet0/0
L 192.168.12.1/32 is directly connected, GigabitEthernet0/0
R1(config)#
```

The configuration above shows R1’s CLI output. We assigned IP addresses to its two interfaces, and the routing table confirms that R1 has direct connections to both the 192.168.1.0/24 and 192.168.12.0/24 networks.

**NOTE: A similar setup was configured on R2 and R3.**

## Assigning Static Routes

On R1, we configure a static route so R1 can reach R3's subnet using R2 as its next hop. This can be done using the `ip route 192.168.3.0 255.255.255.0 192.168.12.2` command, where `192.168.3.0` = destination network and `192.168.12.2` = the next-hop IP that data passes through to reach the desination.

This route allows R1 to forward packets destined for the 192.168.3.0/24 network through R2. R2 and R3 are configured in a similar way, each with static routes pointing toward the networks they are not directly connected to.

<div align="center">
  <img src="https://github.com/user-attachments/assets/f3c272f7-795c-4a6d-8b2a-d6a691ab88c4" width="300" />
  <img src="https://github.com/user-attachments/assets/1d0c5b37-bb45-4263-ace3-98198147158f" width="300" />
</div>


The above image shows the successful pings from **PC1 --> PC2** and **PC2 --> PC1**, proving the static routes were correctly setup on this network.
