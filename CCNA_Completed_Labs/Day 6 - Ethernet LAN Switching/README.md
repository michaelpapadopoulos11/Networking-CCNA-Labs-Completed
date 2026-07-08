# Day 6 - Ethernet LAN Switching

This lab focuses on understanding **ARP**, one of the fundamental processes in Networking. **ARP (Address Resolution Protocol)** is used by devices to map an IP address to a MAC address on a local network. Before any host can send an ICMP ping to another, it must first know what its destination MAC address is. If that mapping isn’t known, the host broadcasts an ARP request, asking “Who has this IP?” The device with that IP responds with its MAC address and both devices can add each others MAC address to their seperate ARP tables, allowing normal communication to proceed.

## Step-by-Step ARP Process:
1. PC1 wants to ping PC3, but it doesn’t know PC3’s MAC address, so it checks its ARP table and finds no entry.
2. PC1 sends an ARP broadcasted to ff:ff:ff:ff:ff:ff as the destination.
3. SW1 receives the broadcast, learns PC1’s MAC on F0/1, and floods the ARP request out all other ports except the one it came one (PC2 and SW2).
4. PC2 receives the ARP request but ignores it because the destination IP isn’t correct.
5. SW2 receives the broadcast frame, learns PC1’s MAC address (adding it to its MAC table), then floods the frame out of F0/1 and F0/2.
6. PC4 receives the ARP request and ignores it for the same reason PC2 did.
7. PC3 receives the ARP request, sees that the target IP matches what's in the destination IP field of the Ethernet Frame and sends an ARP reply back to PC1. It's also important to note that PC3 adds the MAC address of PC2 to its own ARP table at this stage.
8. SW2 forwards the ARP reply towards SW1 using its MAC address table.
9. SW1 forwards the reply to PC1 using its MAC table.
10. PC1 updates its ARP table with PC3’s MAC address.
11. The ICMP ping can now begin and is sent as normal unicast traffic using the learned MAC addresses.

## Topology Screenshot
![day 6 pkt lab](https://github.com/user-attachments/assets/837f5427-bf94-419e-b0f2-8b88f76b9585)
