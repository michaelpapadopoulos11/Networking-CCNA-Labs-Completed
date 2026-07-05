# Day 58 - Wireless LANs

## Network:
<p align="center">
  <img src="https://github.com/user-attachments/assets/a51dad8f-057b-4138-b6a1-8e247111736b" width="550" />
</p>

## 1. Use the 'Web Browser' on PC1 to access the GUI of WLC1.

**Username: admin**
**Password: Cisco123 | NOTE: You must use HTTPS**

<p align="center">
  <img src="https://github.com/user-attachments/assets/25b364c2-ca74-4dea-843f-0f176e640286" width="650" />
</p>

Based on the image above, we’re successfully able to access the Wireless LAN Controller (WLC) web interface on PC1. This was done by going on **PC1 ==> Web Browser ==> Search:** `https://172.16.0.10`.

---

## 2. What is the current state of the Network?

<p align="center">
  <img src="https://github.com/user-attachments/assets/ff43b4ac-d058-4274-892f-0b4d58613915" width="420" />
  <img src="https://github.com/user-attachments/assets/daa6e39f-f393-426c-83df-90eac39f2ac5" width="420" />
</p>

On the left image, the access point is plugged into port 1 — the only active (green) port. The remaining switch ports aren’t in use.
In the right image, both APs have already joined the WLC, even though no wireless clients are currently connected.

---

## 3. Configure Dynamic Interfaces for the Internal & Guest WLANs.

To do this we navigate to the **Controller ==> Interfaces ==> “New” ==> Interface Name: “Internal” | VLAN ID: 100**

I’ve configured some of the following settings:
- **Port Number:** 1
- **IP Address:** 10.0.0.10/24
- **Gateway:** 10.0.0.1
- **Primary DHCP Server:** 10.0.0.1

And select Apply!

<p align="center">
  <img src="https://github.com/user-attachments/assets/4b9c7e2c-8523-4f5d-87a7-4445722e9a13" width="650" />
</p>

As you can see, the new “Internal” dynamic interface now appears. Now I’ll repeat the process for the Guest WLAN.

---

## 4. Create the Internal & Guest WLANs using WPA2+PSK.

Select the **WLANs ==> Go ==> Type: WLAN ==> Profile Name: “Internal” ==> SSID: “Internal” ==> ID: 1**

I’ve also configured a few settings in the Internal WLAN page:
- **Status:** *Check Enabled*
- **Layer 2 Security:** WPA+WPA2
- **WPA+WPA2 Parameters:** *Check WPA2 Policy*
- **Authentication Key Management:** *Check PSK*
- **Password:** “Cisco123”

**I repeated the process for the Guest WLAN.**

---

## 5. Add a wireless client to the network and associate it with an AP.

<p align="center">
  <img src="https://github.com/user-attachments/assets/c9326c34-bc94-40bc-b076-e2ad72760af5" width="650" />
</p>

I've added a smartphone to the topology and connected it to the "Internal" SSID. The WLAN is configured for **WPA/WPA2 security**, and the phone successfully associates with the **Access Point**.

**NOTE: Due to a Packet Tracer bug, DHCP has given the smart phone an IP Address from the VLAN10 Pool even though in a real networking environment the smart phone should’ve been given an address from the VLAN100 Pool.**
