# Day 18 — Multilayer Switching  

This lab focused on replacing a Router‑on‑a‑Stick (ROAS) network with a routed Layer 3 point‑to‑point link, enabling multilayer switching on SW2, configuring SVIs, and verifying full inter‑VLAN and Internet connectivity.

---

## 1. Replace ROAS With a Layer 3 Point‑to‑Point Link

### **R1 Configuration**
I Removed subinterfaces and converted G0/0 into a routed interface.

```plaintext
! Removing ROAS subinterfaces:
no int g0/0.10
no int g0/0.20
no int g0/0.30

! Configuring L3 interface:
interface g0/0
 ip address 10.0.0.194 255.255.255.252
 no shutdown
```

### **SW2 Configuration**
```plaintext
int g1/0/2
 no switchport
 ip address 10.0.0.193 255.255.255.252
 no shutdown

! Enable routing on the multilayer switch:
ip routing

! Setting the default route set to R1:
ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

## 2. Create the SVIs on SW2
```plaintext
interface vlan 10
  ip address 10.0.0.62 255.255.255.192
  (Repeat for VLAN 20 and 30)
```

---
Final Topology:
<img width="1391" height="876" alt="day 18 lab pic" src="https://github.com/user-attachments/assets/9ac087fe-a2c0-42f0-a3a5-beaac4e8d667" />
