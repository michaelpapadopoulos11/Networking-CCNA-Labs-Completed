## Day 15 - Subnetting / VLSM and Configuring Static Routes

In this lab, I was given the network **192.168.5.0/24** and had to subnet it to support four LANs with different host requirements, plus a point‑to‑point link between two routers, R1 and R2.

I used **VLSM** to create appropriately sized subnets for each LAN, then assigned the **first usable IP address** to each PC and the **last usable IP address** to each router interface.

### Subnetting for each LAN:

<p align="center">
  <img src="https://github.com/user-attachments/assets/9d60116f-2fe4-4729-b1b3-66e219f41b11" width="45%" />
  <img src="https://github.com/user-attachments/assets/2fac1f0c-b29a-432b-92c8-48e2c7df67be" width="45%" />
</p>

After completing the addressing, I configured **static routes** on both routers so that R1 can reach LAN3/LAN4, and R2 can reach LAN1/LAN2. The final topology allows every PC to successfully ping every other PC across the network, shown in the screenshot below!
### Final Topology:

<img width="1304" height="876" alt="completed lab screenshot" src="https://github.com/user-attachments/assets/d5c496cf-414e-4d65-87ac-685b7ae680bf" />

**Note:** The first two pings timed out due to ARP. Once the ARP table was populated, all the following pings succeeded.
