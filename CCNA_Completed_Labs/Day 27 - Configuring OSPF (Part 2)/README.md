# Day 27 - Configuring OSPF (Part 2)

### Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/db825cbf-f6b8-41d8-8bfa-508f9836dfbd"
       width="550">
</p>

--- 

## 1. Configure the appropriate hostnames and IP addresses on each device.  Enable router interfaces (You don't have to configure ISPR1)

### R1 Config:
```
Router>en
Router#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
Router(config)#hostname R1
R1(config)#int f1/0
R1(config-if)#ip address 10.0.13.1 255.255.255.252
R1(config-if)#no shut

R1(config-if)#
%LINK-5-CHANGED: Interface FastEthernet1/0, changed state to up

R1(config-if)#int g0/0
R1(config-if)#ip address 10.0.12.1 255.255.255.252
R1(config-if)#no shut

R1(config-if)#
%LINK-5-CHANGED: Interface GigabitEthernet0/0, changed state to up

R1(config-if)#
```

**I won't show the other router configs because they're very similar to this one.**

---

## 2. Configure a loopback interface on each router (1.1.1.1/32 for R1, 2.2.2.2/32 for R2, etc.)

### R1 Config:
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#int l0

R1(config-if)#
%LINK-3-UPDOWN: Interface Loopback0, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Loopback0, changed state to up

R1(config-if)#ip address 1.1.1.1 255.255.255.255
R1(config-if)#
```

**I repeated this config for R2, R3 and R4, just changing the IP address.**

---

## 3. Enable OSPF directly on each interface of the routers. Configure passive interfaces as appropriate

### R1 Config:
```
R1(config)#int range g0/0, f1/0, l0
R1(config-if-range)#ip ospf 1 area 0
R1(config-if-range)#router ospf 1
R1(config-router)#passive-interface l0
R1(config-router)#
```

### R2 Config:
```
R2(config)#int range g0/0, f1/0, l0
R2(config-if-range)#ip ospf 1 area 0
R2(config-if-range)#router ospf 1
R2(config-router)#passive-interface l0
R2(config-router)#
```

Repeat the config for R3 and R4.

---

### 4. Configure the reference bandwidth

To ensure a FastEthernet interface has a cost of **100**, set the reference bandwidth to **10,000 Mbps**.

**Formula:**

*Cost = Reference Bandwidth (Mbps) / Interface Bandwidth of FastEthernet (Mbps)*

**Therefore:** *10,000 / 100 = Cost of 100*

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#router ospf 1
R1(config-router)#auto-cost reference-bandwidth 10000
% OSPF: Reference bandwidth is changed.
        Please ensure reference bandwidth is consistent across all routers.
R1(config-router)#
```

**Repeat this on R2, R3 and R4.**

---

## 5. Configure R1 as an ASBR that advertises a default route in to the OSPF domain.

### R1 Config:
```
R1(config-router)#default-information originate
R1(config-router)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 203.0.113.2
R1(config)#
```

---

## 6. Check the routing tables of R4.  What default route(s) were added?

<p align="center">
  <img src="https://github.com/user-attachments/assets/656cbebb-0442-4964-8467-7696594839ba"
       width="450">
</p>

The default route is highlighted in yellow, it is the path that goes: R4's F1/0 --> R2

---

## Final Topology:

<p align="center">
  <img src="https://github.com/user-attachments/assets/276feef4-4304-48a2-9d71-4d2af9dede6f"
       width="750">
</p>
