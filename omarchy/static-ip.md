# Configure a Static IP on Omarchy (Arch Linux) Using `nmcli`

This guide explains **step by step** how to configure a **static IPv4 address** on Omarchy (Arch Linux–based) using **NetworkManager** and `nmcli`. It is written so **anyone can follow it**, even with minimal networking knowledge.

---

## Prerequisites

* Omarchy / Arch Linux
* NetworkManager installed and running
* Root or sudo access

## Note

* network-ssid is referred to the Network Wifi Connection Name
* Wired Connection 1 is referred to the Ethernet Connection Name

Check NetworkManager:

```bash
systemctl status NetworkManager
```

If not running:

```bash
sudo systemctl enable --now NetworkManager
```

---

## Step 1: Check Existing Connections and Devices

List all saved connections:

```bash
nmcli connection show
```

Check device status:

```bash
nmcli device status
```

You should see something like:

* `wlan0` → Wi‑Fi device
* `eth0` → Wired device
* Active connection name (example: `network-ssid` or `Wired connection 1`)

---

## Step 2: Enable Wi‑Fi (If Needed)

Check radio status:

```bash
nmcli radio
```

Enable Wi‑Fi:

```bash
nmcli radio wifi on
```

Verify Wi‑Fi is not blocked:

```bash
rfkill list
```

---

## Step 3: Connect to Wi‑Fi (DHCP First)

List available Wi‑Fi networks:

```bash
nmcli device wifi list
```

Connect to your Wi‑Fi:

```bash
nmcli device wifi connect "network-ssid" password "YOUR_WIFI_PASSWORD"
```

⚠️ **Important:** Always confirm the connection works with DHCP **before** setting a static IP.

---

## Step 4: Decide Your Static IP Settings

You must know the following values:

| Setting   | Example               |
| --------- | --------------------- |
| Static IP | `192.168.1.115`       |
| Subnet    | `/24` (255.255.255.0) |
| Gateway   | `192.168.1.1`         |
| DNS       | `8.8.8.8`, `1.1.1.1`  |

Make sure the IP is:

* Inside your router subnet
* NOT used by another device

---

## Step 5: Configure Static IP (Wi‑Fi)

Modify the Wi‑Fi connection (example: `network-ssid`):

```bash
sudo nmcli connection modify "network-ssid" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.115/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 1.1.1.1"
```

---

## Step 6: Restart the Connection

Apply changes:

```bash
sudo nmcli connection down "network-ssid"
sudo nmcli connection up "network-ssid"
```

---

## Step 7: Verify Configuration

Check device status:

```bash
nmcli device status
```

Verify IP address:

```bash
ip a
```

Test connectivity:

```bash
ping -c 3 8.8.8.8
ping -c 3 google.com
```

---

## (Optional) Static IP for Wired Connection

If using Ethernet (`Wired connection 1`):

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.method manual \
  ipv4.addresses 192.168.1.115/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns "8.8.8.8 1.1.1.1"

sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

---

## Common Issues & Fixes

### No Internet After Static IP

* Wrong gateway
* IP conflict
* Wrong DNS

Revert to DHCP:

```bash
sudo nmcli connection modify "network-ssid" ipv4.method auto
sudo nmcli connection down "network-ssid"
sudo nmcli connection up "network-ssid"
```

---

## Summary

✔ Uses NetworkManager (`nmcli`)
✔ Works for Wi‑Fi and Ethernet
✔ Persistent after reboot
✔ Production‑safe for servers and desktops

---

**Recommended for Omarchy, Arch Linux, VPS desktops, and production machines.**
