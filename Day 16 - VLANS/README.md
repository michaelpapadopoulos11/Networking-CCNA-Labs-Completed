# Day 16 — Intro to VLANs (Part 1)

This was my first VLAN‑based lab. I started by subnetting the /24 network into three /26 subnets, then configured each PC with the correct IP address, subnet mask (`255.255.255.192`), and the last usable subnet IP address as the default gateway for the router.

## Router (R1) and Switch (SW1) Setup
I connected three straight‑through cables from SW1 to R1, with each router interface acting as the gateway for each VLAN.

On SW1, I created VLAN10, VLAN20, and VLAN30, then assigned the correct ports.

### VLAN10 configuration example:
```bash
int range g0/1, f3/1, f4/1
switchport mode access
switchport access vlan 10
```
^^ I repeated this process for VLAN20 and VLAN30 as well as named them according to the given topology.

### Subnetting and SW1 VLAN Table
<p align="center">
  <img src="https://github.com/user-attachments/assets/0acc2c15-375f-4014-8565-e9a998e6e7bf" height="300">
  <img src="https://github.com/user-attachments/assets/80a0dae4-c9f7-4157-bf64-0b929c037d03" height="300">
</p>

### Final Topology
PC1 is able to successfully ping PC3 in VLAN20 as well as PC6 in VLAN30.

<img width="1391" height="876" alt="pc1 pings pc3 and pc6" src="https://github.com/user-attachments/assets/49a09b63-de06-46ce-b47c-4da802b02c03" />

Note: Initial pings failed due to ARP but they worked every subsequent time!
