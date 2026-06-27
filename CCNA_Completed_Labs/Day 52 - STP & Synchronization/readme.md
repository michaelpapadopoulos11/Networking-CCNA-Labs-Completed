# Day 52 - STP & FHRP Synchronisation

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/a4020e0a-9ed0-4eac-ad42-2e426595e0d1" width="586" height="347">
</p>

For this lab, we will ensure that the HSRP active router is also the STP root bridge, and that the HSRP standby router serves as the STP secondary root for proper synchronisation.

Aligning HSRP with STP ensures that traffic from end‑hosts takes the most direct path toward the default gateway. If the HSRP active router and STP root bridge are not aligned, traffic may end up following a longer, less efficient path.


## 1. Configure HSRP on DSW1/DSW2, and ensure sychronization with STP.

**In VLAN 10:**
**- DSW1 is HSRP active/STP root**
**- DSW2 is HSRP standby/STP secondary root**

### DSW1 Config:
```
DSW1>en
DSW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
DSW1(config)#spanning-tree vlan 10 root primary
DSW1(config)#spanning-tree vlan 20 root secondary
DSW1(config)#
DSW1(config)#do show spanning-tree vlan 10
VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     000C.856A.50BD
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    24586  (priority 24576 sys-id-ext 10)
             Address     000C.856A.50BD
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Gi1/0/1          Desg FWD 4         128.1    P2p
Gi1/0/2          Desg FWD 4         128.2    P2p
Gi1/0/3          Desg FWD 4         128.3    P2p

DSW1(config)#do show spanning-tree vlan 20
VLAN0020
  Spanning tree enabled protocol ieee
  Root ID    Priority    28692
             Address     000C.856A.50BD
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    28692  (priority 28672 sys-id-ext 20)
             Address     000C.856A.50BD
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Gi1/0/1          Desg FWD 4         128.1    P2p
Gi1/0/2          Desg FWD 4         128.2    P2p
Gi1/0/3          Desg FWD 4         128.3    P2p

DSW1(config)#int vlan 10
DSW1(config-if)#standby version 2
DSW1(config-if)#standby 10 ip 10.0.10.254
DSW1(config-if)#
%HSRP-6-STATECHANGE: Vlan10 Grp 10 state Init -> Init

DSW1(config-if)#standby 10 priority 105
DSW1(config-if)#standby 10 preempt
DSW1(config-if)#
DSW1(config-if)#inf 
%HSRP-6-STATECHANGE: Vlan10 Grp 10 state Speak -> Standby

%HSRP-6-STATECHANGE: Vlan10 Grp 10 state Standby -> Act
DSW1(config-if)#int vlan 20
DSW1(config-if)#standby version 2
DSW1(config-if)#standby 20 ip 10.0.20.254
DSW1(config-if)#
%HSRP-6-STATECHANGE: Vlan20 Grp 20 state Init -> Init

DSW1(config-if)#standby 20 priority 95
DSW1(config-if)#
%HSRP-6-STATECHANGE: Vlan20 Grp 20 state Speak -> Standby

%HSRP-6-STATECHANGE: Vlan20 Grp 20 state Standby -> Active

DSW1(config-if)#standby 20 preempt
DSW1(config-if)#end
DSW1#
%SYS-5-CONFIG_I: Configured from console by console

DSW1#
```

**In VLAN 20:**
**- DSW2 is HSRP active/STP root**
**- DSW1 is HSRP standby/STP secondary root**


### DSW2 Config:
```
DSW2>en
DSW2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
DSW2(config)#spanning-tree vlan 10 root secondary
DSW2(config)#spanning-tree vlan 20 root primary
DSW2(config)#
DSW2(config)#do sh spanning-tree vlan 10
VLAN0010
  Spanning tree enabled protocol ieee
  Root ID    Priority    24586
             Address     000C.856A.50BD
             Cost        4
             Port        3(GigabitEthernet1/0/3)
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    28682  (priority 28672 sys-id-ext 10)
             Address     0001.C912.B090
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Gi1/0/1          Desg FWD 4         128.1    P2p
Gi1/0/2          Desg FWD 4         128.2    P2p
Gi1/0/3          Root FWD 4         128.3    P2p

DSW2(config)#do sh spanning-tree vlan 20
VLAN0020
  Spanning tree enabled protocol ieee
  Root ID    Priority    24596
             Address     0001.C912.B090
             This bridge is the root
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec

  Bridge ID  Priority    24596  (priority 24576 sys-id-ext 20)
             Address     0001.C912.B090
             Hello Time  2 sec  Max Age 20 sec  Forward Delay 15 sec
             Aging Time  20

Interface        Role Sts Cost      Prio.Nbr Type
---------------- ---- --- --------- -------- --------------------------------
Gi1/0/1          Desg FWD 4         128.1    P2p
Gi1/0/2          Desg FWD 4         128.2    P2p
Gi1/0/3          Desg FWD 4         128.3    P2p

DSW2(config)#
```

Based on the above config, we can see that DSW2 is the root bridge for VLAN20 and DSW1 is the root bridge for VLAN10 (based on the `Root` port on DSW2’s Gi1/0/3 interface).

### DSW2 Config cont.:
```
DSW2(config)#int vlan 10
DSW2(config-if)#standby version 2
DSW2(config-if)#standby 10 ip 10.0.10.254
DSW2(config-if)#
%HSRP-6-STATECHANGE: Vlan10 Grp 10 state Init -> Init

DSW2(config-if)#standby 10 priority 95
DSW2(config-if)#standby 10 preempy
%HSRP-6-STATECHANGE: Vlan10 Grp 10 state
DSW2(config-if)#standby 10 preempt
DSW2(config-if)#
DSW2(config-if)#
DSW2(config-if)#int vlan 20
DSW2(config-if)#standby version 2
DSW2(config-if)#standby 20 ip 10.0.20.254
DSW2(config-if)#
%HSRP-6-STATECHANGE: Vlan20 Grp 20 state Init -> Init

DSW2(config-if)#standby 20 priority 105
DSW2(config-if)#standby 20 preempt
DSW2(config-if)#
 %HSRP-6-STATECHANGE: Vlan20 Grp 20 state Standby -> Active

DSW2(config-if)#
```
