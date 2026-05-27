# Day 40 - SNMP

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/988e8eec-3e48-4811-b62a-2a3eb3cb40f3" width="70%">
</p>
 
**NOTE : SNMP functionality is VERY limited in Packet Tracer!**

## 1. Configure the following SNMP communities on R1:
### - **read-only: Cisco1**

### - **read/write: Cisco2**

### R1 Config:
```
R1>en
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#snmp-server community Cisco1 ro
%SNMP-5-WARMSTART: SNMP agent on host R1 is undergoing a warm start
R1(config)#snmp-server community Cisco2 rw
R1(config)#
```

---

## 2. Use SNMP 'Get' messages via the MIB browser on PC1 to check the following:

**MIB Setup:**
On PC1 we select: **PC1 ==> Desktop ==> MIB Browser ==> ‘Advanced’** and enter the following details:

<p align="center">
  <img src="https://github.com/user-attachments/assets/e185b512-3f6d-4b96-845a-ccc277962f5f" width="55%">
</p>

### How long has R1 been running? (system uptime)

By following the **SNMP MIB directory** to ‘.sysUpTime’ we can then select the ‘Get’ operation and find the system uptime value of **2 hours and 17 mins**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/775ca4b1-3099-431e-b11c-5ee827afac2b" width="55%">
</p>

### What is the currently configured hostname on R1?

Similarly, we select the ‘.sysName’ option in the **SNMB MIB table** and we can see that the hostname is “**R1**”.

<p align="center">
  <img src="https://github.com/user-attachments/assets/06a60fd8-99eb-4804-9621-84c68ca9b3e3" width="55%">
</p>

### How many interfaces does R1 have?

From the image below we can see that **R1 has 4 interfaces**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/c82fab94-fffd-458c-845b-50e0c0901f95" width="55%">
</p>

### What are those interfaces?

By selecting the ‘.ifDescription’ tab we can see the connected interfaces to **PC1**, that being the **VLAN1 interface** as well as three other connected **GigabitEthernet interfaces**.

<p align="center">
  <img src="https://github.com/user-attachments/assets/ff5a4d77-6212-4267-a62a-97e9604d4809" width="55%">
</p>

**Check what other information you can learn about R1 via SNMP Get messages.**

Some of the other interface MIB tags we can see are:

## Interface SNMP Details

- **.ifType** — Indicates the GigabitEthernet interfaces are using copper **(RJ‑45)** rather than fibre‑optic.
- **.ifMtu** — The Maximum Transmission Unit (MTU) for each interface is 1500 bytes.
- **.ifAdminStatus** — Only **G0/0 is administratively up**; all other interfaces are down.

---

## 3. Use an SNMP 'Set' message from PC1 to change the hostname of R1.

I went back to the ‘.sysName’ tab and changed the Operation type from **‘Get’ ==> ’Set’**. From here we change the Data Type to **“OctetString”** and give **R1** a new name.

<p align="center">
  <img src="https://github.com/user-attachments/assets/b75314b5-e642-4b04-9439-56a729615545" width="55%">
</p>

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#do sh run | include host
hostname R2
R2(config)#
```
Based on the above CLI we can see that **R1s** hostname has been changed to **R2!**
