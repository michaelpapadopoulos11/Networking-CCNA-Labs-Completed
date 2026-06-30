# Day 53 - GRE Tunnels

## Network:
<p align="center">
<img width="744" height="411" alt="d53-netw" src="https://github.com/user-attachments/assets/671c7dfb-29d8-4cb0-88bb-1b0b460f5792" />
</p>

**Notes on the topology:**
- Both routers connect to the Service Provider network, but the GRE tunnel creates a virtual point‑to‑point link between them.
- Traffic between R1 and R2 still travels through the Service Provider, but GRE encapsulates the original packets inside an additional header, forming the tunnel.
- GRE provides encapsulation only. It does not encrypt traffic, so it is not secure on its own and is usually paired with IPsec when security is required.

## 1. Configure a GRE tunnel to connect R1 and R2.

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#interface tunnel 0

R1(config-if)#
%LINK-5-CHANGED: Interface Tunnel0, changed state to up

R1(config-if)#tunnel source g0/0/0
R1(config-if)#tunnel destination 200.0.0.2
R1(config-if)#ip address 192.168.1.1 255.255.255.252
R1(config-if)#
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 100.0.0.1
R1(config)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface Tunnel0, changed state to up

R1(config)#
```

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#interface tunnel 0

R2(config-if)#
%LINK-5-CHANGED: Interface Tunnel0, changed state to up

R2(config-if)#tunnel source g0/0/0
R2(config-if)#tunnel destination 100.0.0.2
R2(config-if)#ip address 192.168.1.2 255.255.255.252
R2(config-if)#exit
R2(config)#ip route 0.0.0.0 0.0.0.0 200.0.0.1
R2(config)#
%LINEPROTO-5-UPDOWN: Line protocol on Interface Tunnel0, changed state to up

R2(config)#
```

---

## 2. Configure OSPF on the tunnel interfaces of R1 and R2, to allow PC1 and PC2 to communicate.

### R1 Config:
```
R1(config)#router ospf 1
R1(config-router)#network 192.168.1.1 0.0.0.0 area 0
R1(config-router)#network 10.0.1.1 0.0.0.0 area 0
R1(config-router)#passive-interface g0/0
R1(config-router)#
```

### R2 Config:
```
R2(config)#router ospf 1
R2(config-router)#network 192.168.1.2 0.0.0.0 area 0
R2(config-router)#network 
02:26:35: %OSPF-5-ADJCHG: Process 1, Nbr 192.168.1.1 on Tunnel0 from LOADING to FULL, Loadi
R2(config-router)#network 10.0.2.1 0.0.0.0 area 0
R2(config-router)#passive-interface g0/0
R2(config-router)#
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/064e74b5-4030-46b9-aaeb-730cc4fe7815" width="360" />
  <img src="https://github.com/user-attachments/assets/90e78bf9-7c1c-4455-bfc2-3c91cf40d8ae" width="360" />
</p>


The image on the left shows a failed ping between **PC1 ==> PC2**, the image on the right shows the successful ping after OSPF had been configured! Through OSPF, R1 was able to learn the **10.0.2.0/24 network** and R2 was able to learn the **10.0.1.0/24 network**.

Setting up OSPF in this situation is advantageous as both R1 and R2 will be able to learn each others neighbouring routes and share routing table info over the GRE tunnel.
