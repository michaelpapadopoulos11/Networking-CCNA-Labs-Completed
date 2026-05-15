# Day 37 - NTP (Network Time Protocol)

In this lab I’ll be setting up all the routers in the network to share the same accurate time. I set their clocks, match their time zones, make R1 get the correct time from the internet, then use R1 as the trusted time source for R2 and R3. I finish by making each router save that time to its hardware clock.

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/e3808847-4a56-42b5-b0fc-c8a020425096" width="650">
</p>

## 1. Configure the software clock on R1, R2, and R3 to 12:00:00 Dec 30 2020 (UTC). Additionally, also configure the time zone of R1, R2, and R3 to match your own.

### R1 Config:
```
R1>en
R1#clock set 12:00:00 Dec 30 2020
R1#show clock detail
12:0:5.159 UTC Wed Dec 30 2020
Time source is user configuration
R1#
R1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R1(config)#clock timezone AEST 10
R1(config)#
```

The `10` is included because the timezone offset from **UTC** to **AEST** is a +10 hour difference.

**(I applied the same configurations on R2 and R3).**

---

## 2. Configure R1 to synchronize to NTP server 1.1.1.1 over the Internet. What stratum is 1.1.1.1? What stratum is R1?

### R1 Config:
```
R1(config)#ntp server 1.1.1.1
R1(config)#
R1(config)#do sh ntp associations

address         ref clock       st   when     poll    reach  delay          offset            disp
*~1.1.1.1       127.127.1.1     1    25       32      377    0.00           0.00              0.24
 * sys.peer, # selected, + candidate, - outlyer, x falseticker, ~ configured
R1(config)#do sh ntp status
Clock is synchronized, stratum 2, reference is 1.1.1.1
nominal freq is 250.0000 Hz, actual freq is 249.9990 Hz, precision is 2**24
reference time is E36A39B3.000003A4 (14:26:59.932 UTC Wed Dec 30 2020)
clock offset is 0.00 msec, root delay is 0.00  msec
root dispersion is 34.15 msec, peer dispersion is 0.24 msec.
loopfilter state is 'CTRL' (Normal Controlled Loop), drift is - 0.000001193 s/s system poll interval is 6, last update was 21 sec ago.
R1(config)#
```

By using the `do sh ntp status` command, we can see that R1 has a **stratum** value of 2. As indicated by the `*` symbol, the NTP association is successfully synced!

---

## 3. Configure R1 as a stratum 8 NTP master. Synchronize R2 and R3 to R1 with authentication. 
NOTE: the 'ntp source' command is not available in Packet Tracer, so just use the physical interface IP addresses of R1.

### R1 Config:
```
R1(config)#ntp master
R1(config)#ntp authenticate
R1(config)#ntp authentication-key 1 md5 password
R1(config)#ntp trusted-key 1
R1(config)#
```

### R2 Config:
```
R2>en
R2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R2(config)#ntp authenticate
R2(config)#ntp authentication-key 1 md5 password
R2(config)#ntp trusted-key 1
R2(config)#
R2(config)#ntp server 192.168.12.1 key 1
R2(config)#
```
The above command configures R1 as R2's NTP server and also specifies that R2 should use key 1 to authenticate R1.

### R3 Config:
```
R3>en
R3#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
R3(config)#ntp authenticate
R3(config)#ntp authentication-key 1 md5 password
R3(config)#ntp trusted-key 1
R3(config)#
R3(config)#ntp server 192.168.13.1 key 1
R3(config)#
```

The above configuration is essentially doing the same as we did to R2 but this time to R3.

---

## 4. Configure NTP to update the hardware calendars of R1, R2, and R3. 

### R1 Config:
```
R1(config)#ntp update-calendar
```
**I repeated this command for R2 and R3.**

**NOTE: you can't view the calendar in Packet Tracer**
