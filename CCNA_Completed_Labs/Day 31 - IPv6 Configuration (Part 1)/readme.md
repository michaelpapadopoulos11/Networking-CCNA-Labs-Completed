# Day 31 - IPv6 Configuration (Part 1)

## Overview
IPv6 is the newer Internet Protocol designed to replace IPv4 as the number of connected devices continues to grow. Its main purpose is to provide a massively larger address space and improve overall network efficiency.

This lab introduces the basics of IPv6 by assigning addresses to the router interfaces and multiple end‑hosts, giving a clear first look at how IPv6 addressing works in a simple network.

## Network Topology:
<p align="center">
  <img src="https://github.com/user-attachments/assets/977c060e-d978-4fca-989b-2df2467e3af7" width="450" />
</p>

## 1. Enable IPv6 routing on R1.

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ipv6 unicast-routing
R1(config)#
```

---

## 2. Configure the appropriate IPv6 addresses on R1.

### R1 Config:
```
R1(config)#
R1(config)#int g0/0
R1(config-if)#ipv6 address 2001:db8:0:1::1/64
R1(config-if)#int g0/1
R1(config-if)#ipv6 address 2001:db8:0:2::1/64
R1(config-if)#int g0/2
R1(config-if)#ipv6 address 2001:db8:0:3::1/64
R1(config-if)#
```

---

## 3. Confirm your configurations. What IPv6 addresses are present on each interface?

Using the `do sh ipv6 int brief` we can view the IPv6 configurations we setup.

<p align="center">
  <img src="https://github.com/user-attachments/assets/872e1858-145f-4d62-a5f5-0bda6954c955" width="450" />
</p>

We can see the the R1 interfaces were setup correctly, as well as the link-local addresses that were automatically made underneath the addresses.

---

## 4. Configure the appropriate IPv6 addresses on each PC. Configure the correct default gateway.

We now assign:

**PC1 -** IPv6 address of 2001:db8:0:1::2/64 | Default Gateway of R1's G0/0 (2001:db8:0:1::1).

**PC2 -** IPv6 address of 2001:db8:0:2::2/64 | Default Gateway of R1's G0/1 (2001:db8:0:2::1)

**PC3 -** IPv6 address of 2001:db8:0:3::3/64 | Default Gateway of R1's G0/2 (2001:db8:0:3::1)

---

## 5. Attempt to ping between the PCs (IPv4 and IPv6)

The image below shows the successful IPv4 and IPv6 ping between PC1 and PC2!

<p align="center">
  <img src="https://github.com/user-attachments/assets/80b5c47c-1656-4867-a799-c64f1e12db7f" width="450" />
</p>
