# Day 21 - STP Configuration and Analysis Lab

This lab focused on identifying the root bridge, configuring primary and secondary STP roots, modifying interface costs and priorities, and enabling PortFast and BPDU Guard on access ports. All work was performed on a pre‑configured Packet Tracer topology.

---

## 1. Identifying the Root Bridge

To determine the root bridge I used the `sh spanning-tree` command on SW2.

<p align="center">
  <img src="https://github.com/user-attachments/assets/28e9f383-7c64-45e0-864c-90cacf1074e0" width="450">
</p>

This image shows us that SW2 is the root bridge in the topology.

---

## 2. Configuring Primary and Secondary Root Bridges

### Configure SW1 as the primary root for VLAN1 and the secondary root for VLAN2. Configure SW2 and the primary root for VLAN2 and the secondary root for VLAN1.


#### SW1 Config:
```
SW1# conf t
SW1(config)# spanning-tree vlan 1 root primary
SW1(config)# spanning-tree vlan 2 root secondary
```

#### SW2 Config:
```
SW2# conf t
SW2(config)# spanning-tree vlan 2 root primary
SW2(config)# spanning-tree vlan 1 root secondary
```

## What is the STP role/state of each port on each switch now?

- For SW1, this is the root bridge for VLAN1 and if the makes SW1 the backup root for VLAN2.
- For SW2, this is the root bridge for VLAN2 and makes SW2 the backup root for VLAN 1.

---

## 3. Increase the VLAN1 cost of SW4's F0/2 interface to 100.

Using the `sh spanning-tree vlan 1` command we can see that the root port of vlan 1 is port Fa0/2.
<p align="center">
  <img src="https://github.com/user-attachments/assets/c8c8df2c-6f75-4fac-853c-ede61fe87a77" width="650">
</p>

Now we will change the cost of VLAN 1 to 100 by entering:
```
SW4#conf t
SW4(config)#int f0/2
SW4(config-if)#span vlan 1 cost 100
SW4(config-if)#
```

After updating the cost of VLAN 1, I used `sh spanning-tree vlan 1` again and can see that the root port has been changed to Fa0/1.

<p align="center">
  <img src="https://github.com/user-attachments/assets/6fa21df1-fc8b-4014-9a89-6bae3904d69a" width="450">
</p>

Fa0/2 is no longer the root port anymore and it's now considered to be in a blocking state. STP always chooses the path with the lowest total cost to the root bridge and therefore Fa0/1 takesover as the root port.

<p align="center">
  <img src="https://github.com/user-attachments/assets/4aa90491-8e33-46d9-b431-d11d62ce2a8e" width="650">
</p>

### STP Cost Comparison

| Interface | Original Cost | Updated Cost | STP Decision |
|----------|----------------|--------------|--------------|
| Fa0/1    | 19             | 19           | Becomes new root port |
| Fa0/2    | 19             | 100          | Moves to blocking state |

--- 

## 4. Increase the VLAN 1 port priority of SW1's F0/1 to 240.

#### SW1 Config:
```
SW1#conf t
SW1(config)#int f0/1
SW1(config-if)#span vlan 1 port-priority 240
SW1(config-if)#
```

### Does SW3 select a different root port? Why/why not?
No, SW3 does not select a different root port. Changing SW1’s Fa0/1 port priority to 240 does not affect the root‑port decision on SW3 because port priority is only used as the last tie‑breaker. Since the path cost to the root didn’t change, and there was no tie, SW3 keeps the same root port (F0/1).

--- 

## 5. Configure PortFast and BPDU Guard on the F0/3 interfaces of SW3/SW4.

#### SW3 Config:
```
SW3#conf t
SW3(config)#int f0/3
SW3(config-if)#spanning-tree portfast
%Warning: portfast should only be enabled on ports connected to a single
host. Connecting hubs, concentrators, switches, bridges, etc... to this
interface  when portfast is enabled, can cause temporary bridging loops.
Use with CAUTION

%Portfast has been configured on FastEthernet0/3 but will only
have effect when the interface is in a non-trunking mode.
SW3(config-if)#spanning-tree bpduguard enable
SW3(config-if)#
```

#### SW4 Config:
```
SW4# conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW4(config)# interface f0/3
SW4(config-if)# spanning-tree portfast
%Warning: portfast should only be enabled on ports connected to a single
host. Connecting hubs, concentrators, switches, bridges, etc... to this
interface  when portfast is enabled, can cause temporary bridging loops.
Use with CAUTION

%Portfast has been configured on FastEthernet0/3 but will only
have effect when the interface is in a non-trunking mode.
SW4(config-if)# spanning-tree bpduguard enable
SW4(config-if)#
```
---

## Final Topology:
<p align="center">
  <img src="https://github.com/user-attachments/assets/d97d380a-98c7-499e-b7e9-6c7c7ece818d" width="700">
</p>
