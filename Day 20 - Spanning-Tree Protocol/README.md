# Day 20 — Spanning Tree Protocol (STP) Analysis

This lab was preconfigured and focused on analysing Spanning-Tree Protocol in a small network. The objective was to identify the root bridge, determine root ports, and classify designated and non‑designated ports.

---

## 1. Identifying the Root Bridge

STP elects the root bridge based on the lowest Bridge ID (priority + MAC address).

- SW3 has the lowest priority value (24,577).
- Therefore, **SW3 is the root bridge**.
- All ports on the root bridge become "designated ports".

---

## 2. Determining the Root Ports

A root port is the port on a non‑root switch with the lowest path cost toward the root bridge.

### SW1
- Two possible paths to SW3: F0/3 and F0/4 (both cost 19).
- STP breaks the tie using the lowest upstream Port ID.
- SW3’s F0/1 has a lower Port ID than F0/2.
  
**SW1 F0/4 becomes the root port.**

### SW2
- Lowest‑cost path from SW2: SW4 → SW3  
  Total cost: 4 + 4 = 8
- **SW2 G0/1 becomes the root port.**
- SW4 G0/1 becomes designated (it faces a root port).

---

## 3. Designated vs Non‑Designated Ports

Each collision domain must have exactly one designated port. The opposite side becomes non‑designated.

### Key Outcomes
- **SW1 F0/3** is non‑designated (it faces the root bridge).
- **SW2 F0/3** is also non‑designated.

### Between SW1 and SW2
- SW1 root cost: 19 (via F0/4)
- SW2 root cost: 8 (via G0/1 → G0/2)
- Lower cost wins designated status.

**SW2 F0/1 and F0/2 = designated ports**  
**SW1 F0/1 and F0/2 = non‑designated ports**

---

## Final Topology

<img width="1005" height="878" alt="Day 20 - Spanning-Tree Protocol" src="https://github.com/user-attachments/assets/09210c14-c36e-43a7-9264-7ccbbb625589" />
