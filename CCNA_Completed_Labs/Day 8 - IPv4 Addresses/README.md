# Day 7/8 - Configuring IPv4 Addresses

## Network Topology:
<p align="center">
  <img width="744" height="286" alt="image" src="https://github.com/user-attachments/assets/46843e79-82a5-4e04-ad12-26e517b07530" />
</p>

In this lab I’ll be configuring the **Router (R1)** to connect three different networks together. Each network has its own IP range (/8, /16 and /24), switch, and PC end-host. I’ll assign IP addresses to R1’s three interfaces, set up the PCs with their correct IPs, and then test connectivity between them using a ping.

### R1 Config:
```
Router>en
Router#conf t
Router(config)#hostname R1
R1(config)#
R1(config)#int g0/0
R1(config-if)#ip add 15.255.255.254 255.0.0.0
R1(config-if)#no shut
R1(config-if)#
R1(config-if)#int g0/1
R1(config-if)#ip add 182.98.255.254 255.255.0.0
R1(config-if)#no shut
R1(config-if)#
R1(config-if)#int g0/2
R1(config-if)#ip add 201.191.20.254 255.255.255.0
R1(config-if)#no shut
R1(config-if)#exit
R1(config)# do sh ip int br
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0     15.255.255.254  YES manual up                    up 
GigabitEthernet0/1     182.98.255.254  YES manual up                    up 
GigabitEthernet0/2     201.191.20.254  YES manual up                    up 
Vlan1                  unassigned      YES unset  administratively down down
R1(config)#
```
The above CLI shows the output of my R1 configs. All I did here was change the hostname to R1, assign IP addresses to the three GigabitEthernet interfaces, turn each interface on with `no shutdown`, and then verify everything by checking the interface status using the `show ip interface brief` command.

## Final Network:

<p align="center">
  <img alt="day 8 ipv4 addresses" src="https://github.com/user-attachments/assets/019dab34-78fb-46b7-9a8c-8006d4e48ee2" />
</p>

The screenshot above shows successful ping requests send to different PCs in the network, proving the router can correctly route the IP addresses.

