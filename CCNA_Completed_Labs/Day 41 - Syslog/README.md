# Day 41 - Syslog

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/1e938223-002d-40a7-9bb9-06037196e707" width="420">
</p>

## 1. Connect to R1's console port using PC2:

I selected **PC2 ==> Desktop ==> Terminal** and configured the following settings as shown below, then I select **OK** to enter R1's console port on PC2. 

<p align="center">
  <img src="https://github.com/user-attachments/assets/9840b83d-81e0-4dc2-86c3-fe197d100227" width="420">
</p>

### [1] Shut down the G0/0 interface. After you receive a syslog message, [2] re-enable the interface. Additionally, What is the severity level of the syslog messages? Also, enable timestamps for logging messages.**

<p align="center">
  <img src="https://github.com/user-attachments/assets/337fa8e5-5d61-4b3a-9cf2-bab10b187252" width="360">
  <img src="https://github.com/user-attachments/assets/16d1885d-e192-4fc6-a832-cac0022a7766" width="360">
</p>

The severity level of each syslog message is **5**. Timestamps are enabled through the `service timestamps log datetime msec` command as shown below.

---

## 2. Telnet from PC1 to R1's G0/0 interface

This is done through accessing **PC1 ==> Desktop ==> Command Prompt ==> `telnet 192.168.1.1` (the IP address of PC1).**

### Enable the unused G0/1 interface

**PC1 Config (Command Prompt):**
```
Cisco Packet Tracer PC Command Line 1.0
C:\>
C:\>telnet 192.168.1.1
Trying 192.168.1.1 …Open

User Access Verification

Username: Jeremy
Password: 
R1>en
Password:
R1#conf t
Enter configuration commands, one per line. End with CTRL/Z.
R1(config)#int g0/1
R1(config-if)#no shut
```

**Why does no syslog message appear?**

By default, syslog messages **do not** appear when connecting via the VTY lines.

**Enable logging to the VTY lines for the current session.**

Logging is enabled through the `do terminal monitor` command, then log message should be displayed.

**NOTE : There is no 'logging monitor' command in packet tracer, but it's enabled by default**
<img width="545" height="613" alt="d41-step-2" src="https://github.com/user-attachments/assets/c1b7993c-6d50-4b3a-99b9-fbd5e200076c" />

---

## 3. Enable logging to the buffer, and configure the buffer size to 8192 bytes.

This can be done through the `logging buffer (buffer size)` command.

**PC1 Config (Command Prompt):**
```
R1(config)#logging buffer 8192
R1(config)#
*Mar 01, 02:35:56.3535: SYS-5-LOG_CONFIG_CHANGE: Buffer logging: level debugging, xml disabled, filtering disabled, size (8192) 
R1(config)#
```

---

## 4. Enable logging to the syslog server SRV1 with a level of 'debugging'.
 
From the output below, R1 will now send syslog messages to SRV1.

```
R1(config)#logging host 192.168.1.100
R1(config)#logging trap debugging
```

To test this, I’ve made some random configs on **R1 via PC1** to output some random syslog messages, the left image below, now if I check **SRV1 ==> Services ==> SYSLOG**, we can view the 
changes that I configured when on **PC1**, the right image below:

<p align="center">
  <img src="https://github.com/user-attachments/assets/1537d282-60b2-4c0a-a21d-41946070205d" width="365">
  <img src="https://github.com/user-attachments/assets/5072c716-5846-4b1e-92f6-eb78d097ca3a" width="360" style="margin-top: 10px;">
</p>
