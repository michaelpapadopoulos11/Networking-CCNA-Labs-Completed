# Day 42 - SSH

## Network:
<img width="623" height="231" alt="d42-netw" src="https://github.com/user-attachments/assets/d7feef2d-9bf2-4b1b-82ff-8b043fd187a9" />


**SW2 has been newly added to the network, but has not yet been configured..**

## 1. Connect Laptop1 to SW2's console port to perform the following configurations:
**Host name: SW2**
**| Enable secret: ccna**
**| Username/PW: jeremy/ccna**
**| VLAN1 SVI: 192.168.2.253/24**
**| Default gateway: R2**

First we connect a *console cable* from Laptop1 to the Switches console port connection. After this we can go on **Laptop1 ==> Desktop ==> Terminal ==> Click “Ok”.** and we can start configuring.
<p align="center">
  <img src="https://github.com/user-attachments/assets/166f7891-8cb9-43a8-af48-4b2200b248dc" width="450" />
</p>

The above image shows the configured hostname, enable secret, user/password, VLAN1 SVI and setting up a default gateway for SW2 via Laptop1.

---

## 2. Configure the following console line security settings on SW2:
**Authentication: Local user**
**Exec timeout: 5 minutes**

### Laptop1 Config:
```
SW2(config)#line con 0
SW2(config-line)#
SW2(config-line)#
SW2(config-line)#login local
SW2(config-line)#exec-timeout 5
SW2(config-line)#end


User Access Verification

Username: jeremy
Password: 

SW2>en
Password: 
SW2#
```

In this step, I secure the console line on SW2 by enabling local login and setting a 5‑minute inactivity timeout.

---


## 3. Configure SW2 for remote access via SSH:
**Domain name: jeremysitlab.com**
**| RSA key size: 2048 bits**
**| Authentication: Local user**
**| Exec timeout: 5 minutes**
**| Protocols: SSH only**
**| + Limit access to PC1 ONLY**

```
SW2#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
SW2(config)#ip domain name jeremysitlab.com
SW2(config)#crypto key generate rsa
The name for the keys will be: SW2.jeremysitlab.com
Choose the size of the key modulus in the range of 360 to 4096 for your
  General Purpose Keys. Choosing a key modulus greater than 512 may take
  a few minutes.

How many bits in the modulus [512]: 2048
% Generating 2048 bit RSA keys, keys will be non-exportable...[OK]

SW2(config)#access-list 1 permit host 192.168.1.1
*Mar 3 0:36:32.598: %SSH-5-ENABLED: SSH 1.99 has been enabled
SW2(config)#line vty 0 15
SW2(config-line)#login local
SW2(config-line)#exec-timeout 5
SW2(config-line)#transport input ssh
SW2(config-line)#access-class 1 in
SW2(config-line)#
```


In the image below, we can see that **R2** is able to ping **SW2** but isn’t able to SSH into it, so the ACL is working as intended. Similarly, **PC1** successfully pings **SW2** and is also able to SSH into it.
<p align="center">
  <img src="https://github.com/user-attachments/assets/6bc03b0f-2ef2-4a2b-97fb-6e4edf133c59" width="450" />
  <img src="https://github.com/user-attachments/assets/ad14b622-c29c-4051-b4a9-254d73787223" width="450" />
</p>
