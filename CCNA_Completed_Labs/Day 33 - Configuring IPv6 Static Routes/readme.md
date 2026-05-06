# Day 33 - IPv6 Static Routes

In this lab, we will configure IPv6 Static Routes in this network to enable PC1 and PC2 to be able to ping each other via R1 => R3 path, as well as configure a backup path via R2.

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/5ec9647d-7515-414c-8ec4-f85b541fec41" width="500" />
</p>

---

## 1. Enable IPv6 routing on each router.

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ipv6 unicast-routing
R1(config)#
```

I repeated this process for R2 and R3.

---

## 2. Use SLAAC to configure IPv6 addresses on the PCs. What IPv6 address was configured on each PC

On PC1, select “Config” and select “Automatic” for the Gateway/DNS IPv6. This will automatically assign a default gateway to PC1 as well as an IPv6 address.

<p align="center">
  <img src="https://github.com/user-attachments/assets/47c0cfcb-10a7-41cb-a666-00e478cccc0f" width="350" />
  <img src="https://github.com/user-attachments/assets/4f556a62-8690-49b9-8126-e9e87e5ae836" width="350" />
</p>

Repeat this for PC2.

---

## 3. Configure static routes on the routers to allow PC1 and PC2 to ping each other. The path via R2 should be used only as a backup path.

We will now setup a static route from R1 to R3 as well as the backup route to R2, we use an **Administrative Distance (AD)** of 5 for the backup route, ensuring it stays as the backup:

### R1 Config:
```
R1(config)#ipv6 route 2001:db8:0:3::/64 g0/1 2001:db8:0:13::2
R1(config)#ipv6 route 2001:db8:0:3::/64 s0/0/0 FE80::20B:BEFF:FED7:4901 5
R1(config)#do sh run | include ipv6 route
ipv6 route 2001:DB8:0:3::/64 GigabitEthernet0/1 2001:DB8:0:13::2
ipv6 route 2001:DB8:0:3::/64 Serial0/0/0 FE80::20B:BEFF:FED7:4901 5
R1(config)#
```

### R2 Config:

From R2 ==> R1 LAN && R2 ==> R3 LAN.

Before configuring this, we first use `do sh ipv6 int br` on R1 and R3 to find the link-local addresses of their S0/0/0 and S0/0/1 interfaces so we can use those addresses for the exit interfaces.

```
R2#en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ipv6 route 2001:db8:0:1::/64 s0/0/0 FE80::202:4AFF:FE23:E201
R2(config)#ipv6 route 2001:db8:0:3::/64 s0/0/1 FE80::290:2BFF:FECC:A101
R2(config)#
```

### R3 Config:

```
R3(config)#ipv6 route 2001:db8:0:1::/64 g0/1 2001:db8:0:13::1
R3(config)#ipv6 route 2001:db8:0:1::/64 s0/0/0 FE80::20B:BEFF:FED7:4901 5
R3(config)#
```

## Final Ping from PC1 ==> PC2:

<p align="center">
  <img src="https://github.com/user-attachments/assets/39146cd2-42d1-466f-b498-f1d87f577ebd" width="450" />
</p>
