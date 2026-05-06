## Day 34 - Standard ACLs

## Network Topology:
<p align="center">
  <img src="https://github.com/user-attachments/assets/910b6add-f1cc-48de-a66e-8aabef83f5d9" width="600" />
</p>

## 1. Configure OSPF on R1 and R2 to allow full connectivity between the PCs and Servers (SRV).

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#router ospf 1
R1(config-router)#network 172.16.0.0 0.0.255.255 area 0
R1(config-router)#network 203.0.113.0 0.0.0.3 area 0
R1(config-router)#
```

We have enabled OSPF on G0/0 and S0/0/0 interfaces, shown through the `do sh ip ospf int` command below.

<p align="center">
  <img src="https://github.com/user-attachments/assets/010ca0df-8edb-4d2a-a9b8-c7216031993e" width="350" />
</p>

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#router ospf 1
R2(config-router)#network 192.168.0.0 0.0.255.255 area 0
R2(config-router)#network 203.0.113.0 0.0.0.3 area 0
R2(config-router)#
```

I setup the OSPF neighbours on R2 and confirmed the neighbour adjacencies using `do sh ip ospf neighbors`.

---

## 2. Configure standard numbered ACLs on R1 and standard named ACLs on R2 to enforce the following network policies:

- **Only PC1 and PC3 can access 192.168.1.0/24**
- **Hosts in 172.16.2.0/24 cannot access 192.168.2.0/24**
- **172.16.1.0/24 cannot access 172.16.2.0/24**
- **172.16.2.0/24 cannot access 172.16.1.0/24**

For better practice, I will configure "Numbered ACLs" on R1 and "Named ACLs on R2".

### R2 Config:
```
R2(config-router)#ip access-list standard TO_192.168.1.0/24
R2(config-std-nacl)#permit 172.16.1.1
R2(config-std-nacl)#permit 172.16.2.1
R2(config-std-nacl)#deny any
R2(config-std-nacl)#
R2(config-std-nacl)#int g0/0
R2(config-if)#ip access-group TO_192.168.1.0/24 out
R2(config-if)#
```

The above named ACL is intended to permit PC1 and PC3 to the `192.168.1.0/24` subnet via an outbound ACL set on R2s G0/0 interface. The image below shows a successful ping between PC1 ==> SRV1 and PC2 ==> SRV1:

<p align="center">
  <img src="https://github.com/user-attachments/assets/4f3658ff-855f-4bfe-94e3-7e9378255a5c" width="350" />
  <img src="https://github.com/user-attachments/assets/86217ad8-6f5d-44ea-821d-a0767cad5536" width="350" />
</p>

The PC2 ==> SRV1 ping fails because it's being blocked by the ACL we setup on R1 and the PC1 ping is successful, meaning the ACL was setup correctly.

*Note: the first ping request sometimes fails due to ARP but it will work every subsequent time.*

### R2 Config:
```
R2(config-if)#ip access-list standard TO_192.168.2.0/24
R2(config-std-nacl)#deny 172.16.2.0 0.0.0.255
R2(config-std-nacl)#permit any
R2(config-std-nacl)#
R2(config-std-nacl)#int g0/1
R2(config-if)#ip access-group TO_192.168.2.0/24 out
R2(config-if)#
```

The above ACL configuration has been setup for R2s G0/1 outbound interface and will deny packets coming from the `172.16.2.0/24` subnet and will permit any other traffic.

The first image shows a ping attempt from PC3 in the `172.16.2.0/24` subnet attempting to ping SRV2 and it fails. The image on the right shows a ping from PC1 (outside of the `172.16.2.0/24` subnet) ==> SRV2 and the ping works correctly, proving our ACL has been setup correctly!

<p align="center">
  <img src="https://github.com/user-attachments/assets/1d5f5d24-6387-44f4-8b73-a35269d0898d" width="350" />
  <img src="https://github.com/user-attachments/assets/c77e62ff-c825-4fab-88ef-50b7be31d6a5" width="350" />
</p>

### R1 Config:
```
R1(config)#access-list 1 deny 172.16.1.0 0.0.0.255
R1(config)#access-list 1 permit any
R1(config)#
R1(config)#access-list 2 deny 172.16.2.0 0.0.0.255
R1(config)#access-list 2 permit any
R1(config)#
R1(config)#do sh access-lists
Standard IP access list 1
    10 deny 172.16.1.0 0.0.0.255
    20 permit any
Standard IP access list 2
    10 deny 172.16.2.0 0.0.0.255
    20 permit any

R1(config)#
R1(config)#int g0/1
R1(config-if)#ip access-group 1 out
R1(config-if)#
R1(config-if)#int g0/0
R1(config-if)#ip access-group 2 out
R1(config-if)#
```

The above ACL ensures that traffic coming from the `172.16.1.0/24` subnet cannot exit outbound onto R1s G0/1 interface. Similarly, traffic coming from the 172.16.2.0/24 subnet cannot exit outbound on R1s G0/0 interface.
