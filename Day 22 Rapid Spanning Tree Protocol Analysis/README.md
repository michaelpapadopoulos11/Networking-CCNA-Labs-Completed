# Day 22 - Rapid STP / Rapid PVST+ Analysis

## 1. Identify the Root Bridge

All switches have the same STP priority value of **32,769**, so the election falls back to the lowest MAC address value.

**SW1** has the lowest MAC address of `0005.5E4E.714B` and is therefore the **Root Bridge**

By running `show spanning-tree` on SW1 we can confirm this, all ports are **Designated**, except **F0/3**, which appears as **Backup (Back)** port.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b908b28a-e42b-4e0f-9963-b87d5ca691c8" width="350">
</p>

---

## 2. Determine Port Roles / States

All port roles and states are shown in the final topology screenshot!

---

## 3. Manually configure the appropriate RSTP link type on each interface.

### SW4 Config:
F0/1 and F0/2 will both be configured as **Point-to-Point** because they're switch-to-switch links.

```
SW4>en
SW4#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW4(config)#int range f0/1 - 2
SW4(config-if-range)#spanning-tree link-type point-to-point
```

F0/24 will become an Edge Port as it's directly connected to an end host.  

```
SW4(config-if-range)#int f0/24
SW4(config-if)#spanning-tree portfast
SW4(config-if)#
```

After inputting the commands I ran `do show spanning-tree`:

<p align="center">
  <img src="https://github.com/user-attachments/assets/00fa6778-97f6-4048-8be4-c0f8c48e70d9" width="350">
</p>

### SW3 Config:
F0/1 and G0/1 were automatically assigned Point-to-Point links after running `sh spanning-tree"` on startup.

Now we just need to assign an edge port to F0/24:

```
SW3>en
SW3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW3(config)#int f0/24
SW3(config-if)#spanning-tree portfast
SW3(config-if)#
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/5f367105-2c64-45a6-9fc9-57896fdd469a" width="350">
</p>

### SW2 Config:
After checking 'sh spanning-tree' on this device all ports are assigned P2P by default, meaning we only need to enable Portfast on F0/23 - 24.

```
SW2>en
SW2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#int range f0/23 - 24
SW2(config-if-range)#spanning-tree portfast
SW2(config-if-range)#
```

### SW1 Configuration

`show spanning-tree` shows **F0/2, F0/3, and F0/24** are **Shared**.  
Even though F0/24 appears as "Shr", it still needs to be configured as an **edge port**.

```
SW1>en
SW1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW1(config)#int f0/24
SW1(config-if)#spanning-tree portfast
SW1(config-if)#
```

<p align="center">
  <img src="https://github.com/user-attachments/assets/094a6f97-fb17-403e-976b-a97d7eca08fa" width="350">
</p>

(Due to a packet tracer bug, it only states "Shr" on F0/24 but it's also an edge port too)

---

## Final Topology:

![day-22-final-top](https://github.com/user-attachments/assets/437d4685-a438-4c12-8bce-4fa42e833d1f)

