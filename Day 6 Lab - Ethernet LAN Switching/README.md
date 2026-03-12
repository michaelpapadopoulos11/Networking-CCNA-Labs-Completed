## Day 6 - Ethernet LAN Switching

This lab was about understanding how ARP works in a small network and how switches learn MAC addresses. I pinged **PC1 → PC3**, which first generated an ARP request that flooded the LAN as an **unknown unicast**. After PC3 replied, both switches learned the correct MAC‑to‑port mappings. The actual ICMP echo request and reply then travelled as **normal unicast traffic**. I checked the updated MAC address tables on SW1 and SW2, and finished the lab by clearing the dynamic MAC entries using `clear mac address-table dynamic`.

![day 6 pkt lab](https://github.com/user-attachments/assets/837f5427-bf94-419e-b0f2-8b88f76b9585)
