# Day 2 – Cable Types and Distance Limits

This lab focuses on choosing the correct cable type when connecting different networking devices. Each connection in the topology is based on real‑world distance limits and the capabilities of copper and fibre‑optic media.

## Notes on Cable Choices

- **R2 ↔ R1 (Copper Crossover)** — The distance is under 100m, so copper is suitable. Because it’s a router‑to‑router connection and we assume Auto MDI‑X is disabled, a crossover cable is required.

- **R1 ↔ R3 (Single‑Mode Fibre)** — The routers are 3km apart. This exceeds the practical range of multimode fibre, so single‑mode fibre is used for long‑distance connectivity.

- **R3 ↔ R4 (Multimode Fibre)** — The distance is 250m. This is too long for UTP (100m max) but well within multimode fibre’s range, so multimode is the appropriate choice here.

## Topology Screenshot
<img width="1392" height="875" alt="Day 02 Flashcards - Interfaces and Cables" src="https://github.com/user-attachments/assets/80468fc1-a043-4b7b-a0c2-401a7b08fccf" />
