# Day 49 - Port Security

## Network:
<img width="508" height="227" alt="d49-netw" src="https://github.com/user-attachments/assets/f55bd174-db6f-455f-96f3-6d1688ceae58" />

## 1. Configure port security on the following interfaces:

**SW1 F0/1, F0/2, F0/3#
Violation mode: Shutdown
Maximum addresses: 1
Sticky learning: Disabled
Aging time: 1 hour**

**#SW2 G0/1#
Violation mode: Restrict
Maximum addresses: 4
Sticky learning: Enabled**


**NOTE: The port‑security defaults already allow 1 MAC address and use shutdown as the violation mode, so I didn’t need to manually set those on SW1.**


Before I could enable port security on F0/1–3, I had to change the ports from dynamic auto to access mode. Port security won’t activate while an interface is in dynamic auto, so switching them to `switchport mode access` was required.

### SW1 Config:
```
SW1>en
SW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#int range f0/1-3
SW1(config-if-range)#switchport port-security aging time 60	
SW1(config-if-range)#sw mode access
SW1(config-if-range)#switchport port-security
SW1(config-if-range)#do sh port-sec int f0/1

Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Shutdown
Aging Time                 : 60 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 1
Total MAC Addresses        : 0
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 0
Last Source Address:Vlan   : 0000.0000.0000:0
Security Violation Count   : 0
```

From the output, I can clearly see that port security is enabled, the aging timer is set to 1 hour, and the violation mode is configured as shutdown.

### SW2 Config:
```
SW2>en
SW2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#int g0/1
SW2(config-if)#switchport port-security violation restrict
SW2(config-if)#switchport port-security maximum 4
SW2(config-if)#switchport port-security mac-address sticky
SW2(config-if)#
SW2(config-if)#switchport mode access
SW2(config-if)#
SW2(config-if)#switchport port-security
SW2(config-if)#
SW2(config-if)#do sh port-security int g0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 4
Total MAC Addresses        : 1
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 1
Last Source Address:Vlan   : 0060.471C.1D19:1
Security Violation Count   : 0

SW2(config-if)#
```

**NOTE: SW2 ends up with four MAC addresses,  three from the PCs and one from SW1.**

After I pinged R1 (10.0.0.254/24) from all three PCs, I checked SW2’s port‑security on G0/1 and confirmed it had dynamically learned all four MAC addresses in the network.

### SW2:
```
SW2(config-if)#do sh port-security int g0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 4
Total MAC Addresses        : 4
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 4
Last Source Address:Vlan   : 0060.471C.1D19:1
Security Violation Count   : 0

SW2(config-if)#
```

---

## 2. Trigger port security violations on SW1 and SW2 (for example by connecting another PC) and observe the actions taken by each switch.

I configured an IP for VLAN 1 on SW1 virtual interface and pinged R1, the ping should fail.

### SW1:
```
SW1(config-if-range)#int vlan 1
SW1(config-if)#
SW1(config-if)#ip address 10.0.0.10 255.255.255.0
SW1(config-if)#no shut

SW1(config-if)#
%LINK-3-UPDOWN: Interface Vlan1, changed state to down

%LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to up

SW1(config-if)#do ping 10.0.0.254

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.0.0.254, timeout is 2 seconds:
.....
Success rate is 0 percent (0/5)

SW1(config-if)#
```

SW2 is blocking the ping because the MAC address I used isn’t one of the allowed ones. When I check the port‑security details on SW2’s G0/1 interface, I can see exactly what happened, as shown below.  ### SW2:
```
SW2(config)#
SW2(config)#do sh port-security int g0/1
Port Security              : Enabled
Port Status                : Secure-up
Violation Mode             : Restrict
Aging Time                 : 0 mins
Aging Type                 : Absolute
SecureStatic Address Aging : Disabled
Maximum MAC Addresses      : 4
Total MAC Addresses        : 4
Configured MAC Addresses   : 0
Sticky MAC Addresses       : 4
Last Source Address:Vlan   : 0060.471C.1D19:1
Security Violation Count   : 6

SW2(config)#
```

We can see that the violation counter has gone up from 0 to 6 (at the bottom). Normally a syslog message should appear however this is a limitation of Packet Tracer.
