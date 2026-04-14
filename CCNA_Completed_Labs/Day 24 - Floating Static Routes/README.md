## Day 24 - Floating Static Routes

This lab demonstrates how to use floating static routes as backup paths when the primary OSPF link fails, ensuring end-hosts can still connect to each other even during a router‑to‑router link outage.

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/f79db3d3-d5bb-46fd-9055-9e371780fb77" width="550">
</p>

## 1. Check the routing tables of R1 and R2.  

<p align="center">
  <img src="https://github.com/user-attachments/assets/b7fd95b1-2726-431c-9a5d-be9627592f64" width="450">
</p>

### Which dynamic routing protocol is Enterprise A using?

by using `show ip route` on R1, we can see that enterprise A is using OSPF as its dynamic routing protocol.

### Which route will be used if PC1 tries to access SRV1?

PC1 will use the OSPF route 10.0.2.0/25 over G0/2/0 to access SRV1.

### Which route will be used if PC1 tries to access remote server 1.1.1.1 over the Internet?

It will use the default route to ISPB:  `S* 0.0.0.0/0 [1/0] via 203.0.113.9`

### Test by pinging SRV1 and 1.1.1.1

<p align="center">
  <img src="https://github.com/user-attachments/assets/721bd761-61db-4c55-94c5-46e3c2555d9c" width="450">
</p>

---

## 2. Configure floating static routes on R1 and R2 that allow PC1 to reach SRV1 if the link between R1 and R2 fails.

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip route 10.0.2.0 255.255.255.0 203.0.113.1 111
```

We have configured a static route to reach the 10.0.2.0/24 destination network through the 203.0.113.1 network. The `111` is the administrative distance that we are manually setting as this is supposed to be a backup route! The default OSPF route that was pre-configured was set to 110 AD, therefore our route needed to be set to a higher value so it's **not** the preferred route.

Now we can setup R2 in a similar way to R1's setup.

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ip route 10.0.1.0 255.255.255.0 203.0.113.5 111
```

### Do the routes enter the routing tables of R1 and R2?

No, the routes don't appear in the routing table of R1 and R2 because the route with the AD of 110 takes preference over our configured routes with an AD of 111.

---

## 3. Shut down the G0/2/0 interface of R1 or R2.

### Do the floating static routes enter the routing tables of R1 and R2?

Yes, after using the `shut` command is used on G0/2/0 on R1 and R2, our configured routes do appear, with the AD value of 111.

### Ping from PC1 to SRV1 to confirm.

After shutting G0/2/0 from R1 and R2 the backup route ping is successful!

<p align="center">
  <img src="https://github.com/user-attachments/assets/021d2a45-8ca8-44f4-b394-316b82a872b9" width="450">
</p>

---

## Final Topology: 

<p align="center">
  <img src="https://github.com/user-attachments/assets/2d86a95e-fc38-4edc-9254-d7a4fc94a58e" width="550">
</p>
