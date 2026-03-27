# Day 17 — VLANs (Part 2), Trunking & 'Router on a stick'

## Step 1 — VLAN Configuration

On **SW1**, configured access ports for **VLAN10**:

    int range f0/1 - 2
  
    switchport mode access
  
    switchport access vlan 10

I repeated this process for VLAN30 and its 2 interfaces, F0/3 and F0/4. After this, I configured **SW2** the same way for **VLAN10** and **VLAN20**.

---

## Step 2 — Trunking Between SW1 & SW2
I enabled trunking on **g0/1** on both switches:
  
      switchport mode trunk
    
      switchport trunk allowed vlan 10,30

NOTE: **VLAN20** was excluded because SW1 has no VLAN20 hosts on.

Set an unused VLAN as the native VLAN:

      switchport trunk native vlan 1001
    
VLAN30 (`vlan 30`) was created on SW2 so it would appear in the trunk table.

Verified trunk status:
  
      show interface trunk

<p align="center">
  <img width="369" height="177" alt="SW2 trunk table" src="https://github.com/user-attachments/assets/c1120124-0760-4f7f-aee2-d85669d4a383" />
</p>

---

## Step 3 — Trunk to Router (ROAS)

**On SW2**:

      SW2(config)#int g0/2
      SW2(config-if)#sw mode trunk
      SW2(config-if)#sw trunk allowed vlan 10,20,30
      SW2(config-if)#sw trunk native vlan 1001
      SW2(config-if)#

Configured the link between **SW2 ↔ R1** as a trunk for ROAS.

**On R1**:

      R1(config)#int g0/0.10
      R1(config-subif)#encapsulation dot1q 10
      R1(config-subif)#ip address 10.0.0.62 255.255.255.192

(Repeat for VLAN20 and VLAN30 with their correct subinterfaces + IPs)

**Final Topology**:
The final topology shows successful pings between PC1 -> PC7 (VLAN10 -> VLAN 10), PC1 -> PC3 (VLAN10 -> VLAN30) and PC1 -> PC5 (VLAN10 -> VLAN20).

<img width="1391" height="876" alt="successful pings" src="https://github.com/user-attachments/assets/bc4bc606-a6fd-4063-bb49-d72a4dc65ae4" />


