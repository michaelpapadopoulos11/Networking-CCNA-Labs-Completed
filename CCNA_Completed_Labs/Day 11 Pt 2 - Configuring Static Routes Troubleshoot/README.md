# Day 11 (Part 2) – Configuring Static Routes (Troubleshooting)

## Network Topology:
<div align="center">
  <img src="https://github.com/user-attachments/assets/627fd85a-4dec-4f7f-b724-07a062491440" width="600">
</div>

This lab reuses the same Network Topology from [Day 11 (Part 1)](https://github.com/michaelpapadopoulos11/Networking-CCNA-Labs-Completed/tree/main/CCNA_Completed_Labs/Day%2011%20Pt%201%20-%20Configuring%20Static%20Routes), but with several misconfigurations added to simulate real troubleshooting scenarios. The goal was to work through each router, identify any issues with them and restore full end‑to‑end connectivity using show commands, interface checks, and assigning correct static routes.

---

## Initial Checks
The first thing I did was confirm that all router interfaces were up and assigned to the correct IP addresses.  
During this check, I noticed that **R3’s G0/0 interface was misconfigured**, it was set to `192.168.23.3/24` instead of  `192.168.13.3/24` which it should be. After correcting the address, I moved on to reviewing the routing tables.

- **R1** should have **one** static route assigned.  
- **R2** should have **two** static routes assigned. 
- **R3** should have **one** static route assigned.

---

## Router Fixes:

### R1
- The routing table showed a static route pointing to the correct destination network, but with the **wrong next hop** address.
- I removed the incorrect route via `no ip route 192.168.3.0 255.255.255.0 192.168.12.3` and added the correct route, `ip route 192.168.3.0 255.255.255.0 192.168.12.2`.

### R2
- The route to `192.168.1.0/24` was correct, but the route to `192.168.3.0/24` was incorrectly shown as being **"directly connected to G0/0"**.
- I removed the incorrect entry via `no ip route 192.168.3.0 255.255.255.0 g0/0` and added the correct entry of `ip route 192.168.3.0 255.255.255.0 192.168.13.3`.

### R3
- R3 had **no static routes** assigned to it after checking its routing table, so I added the missing route to reach R1’s subnet, `ip route 192.168.1.0 255.255.255.0 192.168.13.2`.

---

<div align="center">
  <img src="https://github.com/user-attachments/assets/f3c272f7-795c-4a6d-8b2a-d6a691ab88c4" width="300" />
  <img src="https://github.com/user-attachments/assets/1d0c5b37-bb45-4263-ace3-98198147158f" width="300" />
</div>


The above image shows the successful pings from **PC1 --> PC2** and **PC2 --> PC1**, proving the end-to-end connectivity in the network works now!
