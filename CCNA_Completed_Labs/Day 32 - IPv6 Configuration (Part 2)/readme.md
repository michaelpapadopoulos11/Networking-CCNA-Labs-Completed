# Day 32 - Configuring IPv6 (Part 2)

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/d4929200-90c2-4866-ae51-1103ea830533" width="650">
</p>

---

## 1. Use EUI-64 to configure IPv6 addresses on G0/1 of R1/R2. Before configuring the addresses, calculate the EUI-64 interface ID that will be generated on each interface.

### R1 Config:
```
R1>en
R1#show int g0/1
GigabitEthernet0/1 is up, line protocol is up (connected)
  Hardware is CN Gigabit Ethernet, address is 0030.f236.4502 (bia 0030.f236.4502)
  Internet address is 10.0.1.254/24
  MTU 1500 bytes, BW 1000000 Kbit, DLY 10 usec,
     reliability 255/255, txload 1/255, rxload 1/255
  Encapsulation ARPA, loopback not set
  Keepalive set (10 sec)
  Full-duplex, 100Mb/s, media type is RJ45
  output flow-control is unsupported, input flow-control is unsupported
  ARP type: ARPA, ARP Timeout 04:00:00, 
  Last input 00:00:08, output 00:00:05, output hang never
  Last clearing of "show interface" counters never
  Input queue: 0/75/0 (size/max/drops); Total output drops: 0
  Queueing strategy: fifo
  Output queue :0/40 (size/max)
  5 minute input rate 0 bits/sec, 0 packets/sec
  5 minute output rate 0 bits/sec, 0 packets/sec
     0 packets input, 0 bytes, 0 no buffer
     Received 0 broadcasts, 0 runts, 0 giants, 0 throttles
     0 input errors, 0 CRC, 0 frame, 0 overrun, 0 ignored, 0 abort
     0 watchdog, 1017 multicast, 0 pause input
     0 input packets with dribble condition detected

R1#
```

From the `sh int g0/1` output we can see that the MAC address for R1’s g0/1 is: 0030.f236.4502.

***Calculating EUI-64 for R1 G0/1:***
1. 0030.f236.4502 — Initial MAC address
2. 0030.f2  |  36.4502 — Split the address in half
3. 0030.f2ff    fe36.4502 — Add in the `fffe` to the middle
4. 0230.f2ff.fe36.4502 — invert the 7th bit, converting the `0` to a `2`.

### Continued R1 Config:
```
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ipv6 unicast-routing
R1(config)#int g0/1
R1(config-if)#ipv6 address 2001:db8::/64 eui-64
R1(config-if)#do sh ipv6 int
GigabitEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::230:F2FF:FE36:4502
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8::230:F2FF:FE36:4502, subnet is 2001:DB8::/64 [EUI]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FF36:4502
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
R1(config-if)#
```

From here, we can see that the EUI-64 address correctly displays to the one we calculated as well as a link-local address that was automatically made when enabling IPv6 on the router.


***Calculating EUI-64 for R2 G0/1:***
1. 0001.63b0.b802 — Initial MAC address
2. 0001.63  |  b0.b802 — Split the address in half
3. 0001.63ff    feb0.b802 — Add in the `fffe` to the middle
4. 0201.63ff.feb0.b8 02 — invert the 7th bit, converting the `0` to a `2`.
5. Final : 2001:db8:0:1:201:63ff:feb0:b802

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ipv6 unicast-routing
R2(config)#int g0/1
R2(config-if)#ipv6 address 2001:db8:0:1::/64 eui-64
R2(config-if)#do sh ipv6 int
GigabitEthernet0/1 is up, line protocol is up
  IPv6 is enabled, link-local address is FE80::201:63FF:FEB0:B802
  No Virtual link-local address(es):
  Global unicast address(es):
    2001:DB8:0:1:201:63FF:FEB0:B802, subnet is 2001:DB8:0:1::/64 [EUI]
  Joined group address(es):
    FF02::1
    FF02::2
    FF02::1:FFB0:B802
  MTU is 1500 bytes
  ICMP error messages limited to one every 100 milliseconds
  ICMP redirects are enabled
  ICMP unreachables are sent
  ND DAD is enabled, number of DAD attempts: 1
  ND reachable time is 30000 milliseconds
  ND advertised reachable time is 0 (unspecified)
  ND advertised retransmit interval is 0 (unspecified)
  ND router advertisements are sent every 200 seconds
  ND router advertisements live for 1800 seconds
  ND advertised default router preference is Medium
  Hosts use stateless autoconfig for addresses.
R2(config-if)#
```

---

## 2. Configure the appropriate IPv6 addresses/default gateways on PC1 and PC2.

Configuring PC1:
- Assign the Default Gateway of R1 :  2001:db8::230:f2ff:fe36:4502
- Assign the IPv6 Address to PC1 : 2001:db8::2/64

Configuring PC2:
- Assign the Default Gateway of R2 : 2001:db8:0:1:201:63ff:feb0:b802
- Assign the IPv6 Address to PC2 : 2001:db8:0:1::2/64

---

## 3. Enable IPv6 on G0/0 of R1/R2 without explicitly configuring an IPv6 address.


### R1 Config:
```
Int g0/0
Ipv6 enable
```

### R2 Config:
```
Int g0/0
Ipv6 enable
```

This will automatically create 2 link-local addresses on both routers, giving them IPv6 addresses without explicitly configuring IPv6 addresses.
