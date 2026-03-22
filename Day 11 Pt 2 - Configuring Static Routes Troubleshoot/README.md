## Day 11 (Part 2) – Configuring Static Routes (Troubleshooting)

In this lab, several routing and addressing errors were intentionally introduced. I worked through each router to identify and correct the issues so that end‑to‑end connectivity could be restored.

### R1
- Found an incorrect static route using `show ip route`.
- Removed the bad entry and added the correct next‑hop for the `192.168.3.0/24` network.

### R2
- The static route for `192.168.3.0/24` was pointing to the wrong interface.
- Deleted the incorrect route and re‑added it using the correct outgoing interface.

### R3
- Corrected a misconfigured IPv4 address (`192.168.23.3` → `192.168.13.3`).

## Topology Screenshot
<img width="1245" height="875" alt="Configuring Static Routing Troubleshoot" src="https://github.com/user-attachments/assets/8781cee5-3059-43b3-8677-f4bb0fdc308c" />
