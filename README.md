<div align="center">

# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

![Skill](https://img.shields.io/badge/Skill-Cybersecurity-C00000)
![VirtualBox](https://img.shields.io/badge/VirtualBox-7.2.16-0B6BCB)
![Kali Linux](https://img.shields.io/badge/Kali%20Linux-2026.3-black)
![Network](https://img.shields.io/badge/Network-10.0.0.0%2F24-14A085)
![Virtualization](https://img.shields.io/badge/Skill-Virtualization-C00000)
![Networkwalks](https://img.shields.io/badge/Program-Networkwalks-555555)

</div>

---

## 📌 Project Overview

This project sets up a **virtual cybersecurity and penetration-testing laboratory** on my own laptop using **VirtualBox** and **Kali Linux**.

The purpose of the lab is to create a controlled environment where security tools, network scanning, reconnaissance and vulnerability assessment can be practiced safely and repeatedly.

The lab runs on a private NAT Network, so additional virtual machines can be added later and used as targets for authorized security testing.

> **Week 1 – Project Module 1 (WK1-PM1)** · Networkwalks Cybersecurity Internship Program

---

## ✅ Task Requirements & Status

| Requirement | Implementation | Status |
|---|---|---|
| Use VirtualBox as base (latest recommended version) | Oracle VirtualBox 7.2.16 | ✅ |
| Set up Kali Linux as attacking machine | Kali Linux 2026.3 (VirtualBox amd64 image) | ✅ |
| Network in subnet 10.0.0.0/24 using NATNetwork | NAT Network `NatNetwork`, 10.0.0.0/24, DHCP enabled | ✅ |
| Clipboard & drag/drop enabled | Shared Clipboard and Drag-and-Drop: Bidirectional | ✅ |
| Shared folder with host Downloads | Host `Downloads` shared, Full access, Auto Mount | ✅ |
| Kali IP address 10.0.0.2/24 | Static IP 10.0.0.2/24, gateway 10.0.0.1 | ✅ |
| Kali has full Internet access | Ping to google.com and 8.8.8.8, 0% packet loss | ✅ |
| Snapshot of the VM | `Snapshot 1` – "WK-1 Completed" | ✅ |

---

## 🗺️ Lab Architecture

| Component | Details |
|---|---|
| Host OS | Windows laptop/PC |
| Hypervisor | Oracle VirtualBox 7.2.16 |
| Attacker VM | Kali Linux 2026.3, 2048 MB RAM |
| Virtual network | `NatNetwork` – 10.0.0.0/24, DHCP enabled, IPv6 disabled |
| Kali IP | Manual: 10.0.0.2/24 · Gateway 10.0.0.1 · DNS 8.8.8.8 |
| Network adapter | Adapter 1 → NAT Network, Intel PRO/1000 MT Desktop (82540EM), Promiscuous Mode: Allow All |
| Host integration | Bidirectional clipboard & drag-and-drop, shared Downloads folder |

---

## 🛠️ Implementation Steps

### Step 1 – Install 7-Zip and VirtualBox

7-Zip was installed to extract the compressed Kali Linux VM image, and the latest Oracle VirtualBox was installed on the host.

![VirtualBox version](images/01-virtualbox-version.png)

*VirtualBox About window showing version 7.2.16.*

### Step 2 – Create the NAT Network (10.0.0.0/24)

In VirtualBox Manager: **File → Tools → Network → NAT Networks → Create**.

- **Name:** `NatNetwork`
- **IPv4 Prefix:** `10.0.0.0/24`
- **Enable DHCP:** checked · **Enable IPv6:** unchecked

![NAT Network](images/02-nat-network.png)

### Step 3 – Import and configure the Kali Linux VM

The Kali Linux VirtualBox image was downloaded from [kali.org](https://www.kali.org/get-kali/), extracted and imported into VirtualBox.

**3.1 Network adapter** – Adapter 1 attached to `NAT Network` → `NatNetwork`, Promiscuous Mode set to *Allow All*, virtual cable connected.

![VM network adapter](images/03-vm-network-adapter.png)

**3.2 Clipboard and drag-and-drop** – *General → Features*: Shared Clipboard and Drag-and-Drop set to **Bidirectional**.

![Clipboard and drag-drop](images/04-clipboard-dragdrop.png)

**3.3 Shared folder** – the host `Downloads` folder is added as a global shared folder (Full access, Auto Mount). Inside Kali it appears on the desktop as `sf_Downloads`.

![Shared folder](images/05-shared-folder.png)

### Step 4 – Configure the static IP in Kali Linux

Network icon → **Edit Connections** → *Wired connection 1* → **IPv4 Settings**:

- **Method:** Manual
- **Address / Netmask / Gateway:** `10.0.0.2` / `24` / `10.0.0.1`
- **DNS servers:** `8.8.8.8` (use `10.0.0.1` if Internet problems occur)

![Kali static IP](images/06-kali-static-ip.png)

### Step 5 – Fix Internet connectivity (VirtualBox 7 / Kali 2026.x)

After setting a static IP, the VM had no Internet. This is a known issue with VirtualBox 7 and Kali Linux 2026.1+, fixed by disabling the IPv4 duplicate address detection timeout and restarting the connection:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

![nmcli fix](images/07-nmcli-fix.png)

### Step 6 – Verify configuration and connectivity

`ip a` shows `eth0` with `10.0.0.2/24`. Connectivity was tested with ping to google.com, the gateway and 8.8.8.8:

```bash
ip a
ping -c 4 google.com
ping -c 4 10.0.0.1
ping -c 4 8.8.8.8
```

![ip a, ping google, ping gateway and DNS](images/08-ip-a-ping-google-ping-gateway-dns.png)


| Test | Received | Packet loss | Avg RTT |
|---|---|---|---|
| `ping google.com` | 4 / 4 | 0% | 23.4 ms |
| `ping 10.0.0.1` (gateway) | 4 / 4 | 0% | 0.62 ms |
| `ping 8.8.8.8` | 4 / 4 | 0% | 25.2 ms |

### Step 7 – Take a snapshot of the VM

A snapshot named **Snapshot 1** (description: *WK-1 Completed*) was taken so the lab can be restored to this known-good state at any time.

![Take snapshot](images/10-take-snapshot.png)

![Snapshot list](images/11-snapshot-list.png)

---

## 🧯 Troubleshooting Experience

| Item | Description |
|---|---|
| **Problem** | After assigning the static IP 10.0.0.2/24, Kali Linux had no Internet access. |
| **Cause** | Known issue with VirtualBox 7 + Kali Linux 2026.1 or newer: NetworkManager waits for duplicate address detection before the connection is fully online. |
| **Checks** | NatNetwork created correctly, adapter attached to it, IP/gateway/DNS correct, no other VM using 10.0.0.2. |
| **Solution** | Ran the three `nmcli` commands in Step 5, then restarted Kali Linux. |
| **Result** | Ping to gateway, 8.8.8.8 and google.com all succeeded with 0% packet loss. |
| **Other tips** | Set DNS to `10.0.0.1`, check host firewall/antivirus, restart VMs and host OS, or test another IP such as `10.0.0.99/24` to rule out an address conflict. |

---

## 🎓 Key Learnings

- Built an isolated lab with Kali Linux on a dedicated NAT Network.
- Configured a static IP with NetworkManager and verified it with `ip a` and `ping`.
- Used `nmcli` to troubleshoot a connectivity issue.
- Used snapshots to protect a working configuration.

---

## ⚖️ Ethical Use Disclaimer

This lab is for **education and research only** and is used only on my own devices and in this isolated environment, or where I have explicit written permission. Unauthorized access to systems is illegal.

---

<div align="center">

**Networkwalks Cybersecurity Program** · Instructor: Waqas Karim CCIE

</div>
