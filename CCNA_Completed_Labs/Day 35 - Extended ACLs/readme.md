# Day 35 - Extended ACLs

## Network
<img width="1138" height="381" alt="day-35-netw" src="https://github.com/user-attachments/assets/0b4bbf49-d135-4c9d-a4e0-c921bde2d35a" />

In this lab we've been given a network topology and it's up to us to configure an extended ACL to setup some inbound and outbound routing rules for the networks traffic flow.

---

## 1. Configure extended ACLs to fulfill the following network policies.
**ACL Policies:**
1. Hosts in 172.16.2.0/24 can't communicate with PC1.
2. Hosts in 172.16.1.0/24 can't access the DNS service on SRV1.
3. Hosts in 172.16.2.0/24 can't access the HTTP or HTTPS services on SRV2.

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip access-list extended 100
R1(config-ext-nacl)#deny udp 172.16.1.0 0.0.0.255 host 192.168.1.100 eq 53
R1(config-ext-nacl)#deny tcp 172.16.1.0 0.0.0.255 host 192.168.1.100 eq 53
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#
R1(config-ext-nacl)#int g0/0
R1(config-if)#ip access-group 100 in
R1(config-if)#
```
<p align="center">
  <img width="603" height="553" alt="d35-ping" src="https://github.com/user-attachments/assets/28be75f7-5c1c-438e-bf40-95f84a99a539" />
</p>

The above image shows a ping from PC1 (in the 172.16.1.0/24 network) unsuccesfully pinging SRV2 through DNS however PC1 is still able to ping SRV2 through its IP address. This satisfies the second requirement!

### R1 Config:
```
R1(config)#ip access-list extended 101
R1(config-ext-nacl)#deny ip 172.16.2.0 0.0.0.255 host 172.16.1.1
R1(config-ext-nacl)#deny tcp 172.16.2.0 0.0.0.255 host 192.168.2.100 eq 80
R1(config-ext-nacl)#deny tcp 172.16.2.0 0.0.0.255 host 192.168.2.100 eq 443
R1(config-ext-nacl)#permit ip any any
R1(config-ext-nacl)#
R1(config-ext-nacl)#int g0/1
R1(config-if)#ip access-group 101 in
R1(config-if)#
```

The above configuration is for policies 1 and 3, blocking all traffic from 172.16.2.0/24 to PC1 and preventing that same subnet from accessing HTTP/HTTPS services on SRV2.

<p align="center">
  <img width="605" height="585" alt="d35-ping2" src="https://github.com/user-attachments/assets/15788982-9491-4992-bdc6-e910132bfbba" />
</p>
This PC3 ping confirms that ACL 101 is working as intended:  traffic from the 172.16.2.0/24 subnet is successfully blocked from reaching PC1 while normal connectivity to other hosts remains unaffected.
