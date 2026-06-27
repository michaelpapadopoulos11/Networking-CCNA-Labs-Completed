# Day 51 - Dynamic ARP Inspection

## Network:
<img width="525" height="222" alt="d51-netw" src="https://github.com/user-attachments/assets/4af56fde-1070-49ed-9696-2e138f9dc8b2" />

## 1. Configure R1 as a DHCP server. Exclude 192.168.1.1 - 192.168.1.9 from the pool | Default gateway: R1

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.9
R1(config)#ip dhcp pool POOL1
R1(dhcp-config)#network 192.168.1.0 255.255.255.0
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#exit
R1(config)#
```

In the above CLI, I set up R1 as a DHCP server by excluding the first nine IPs and creating a pool that hands out addresses on the 192.168.1.0/24 network with 192.168.1.1 as the default gateway.

---

## 2. Configure DHCP snooping on SW1 and SW2.

### SW1 Config:
```
SW1>en
SW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#ip dhcp snooping
SW1(config)#ip dhcp snooping vlan 1
SW1(config)#no ip dhcp snooping information option
SW1(config)#
SW1(config)#int g0/2
SW1(config-if)#ip dhcp snooping trust
SW1(config-if)#exit
SW1(config)#
```

### SW2 Config:
```
SW1>en
SW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#ip dhcp snooping
SW1(config)#ip dhcp snooping vlan 1
SW1(config)#no ip dhcp snooping information option
SW1(config)#
SW1(config)#int g0/2
SW1(config-if)#ip dhcp snooping trust
SW1(config-if)#exit
SW1(config)#
```

I enabled DHCP snooping on both SW1 and SW2, applied it to VLAN 1, then disabled Option 82, and marked the uplink interface as trusted so DHCP traffic can pass correctly.

---

## 3. Configure DAI on SW1 and SW2 | Enable all additional validation checks. | Trust ports connected to a router or switch

### SW1 Config:
```
SW1(config)#ip arp inspection vlan 1
SW1(config)#ip arp inspection validate dst-mac ip src-mac
SW1(config)#
SW1(config)#int range g0/1-2
SW1(config-if-range)#ip arp inspection trust
SW1(config-if-range)#end
SW1#
%SYS-5-CONFIG_I: Configured from console by console

SW1#
```


### SW2 Config:
```
SW2(config)#ip arp inspection vlan 1
SW2(config)#ip arp inspection validate ip src-mac dst-mac
SW2(config)#
SW2(config)#int g0/1
SW2(config-if)#ip arp inspection trust
SW2(config-if)#end
SW2#
%SYS-5-CONFIG_I: Configured from console by console

SW2#
```

Based on the above CLI, Dynamic ARP Inspection was enabled on both SW1 and SW2, turned on all the extra validation checks (`ip`, `src-mac`, `dst-mac`), and trusted the uplink interfaces so ARP traffic from other switches/routers is allowed.

From the image below, we can see that PC1 has been assigned an IP Address through DHCP and is successfully able to ping its default gateway.

<p align="center">
  <img src="https://github.com/user-attachments/assets/81d6a833-198f-4fff-8a42-137f24c1d3cd" width="350" />
</p>
