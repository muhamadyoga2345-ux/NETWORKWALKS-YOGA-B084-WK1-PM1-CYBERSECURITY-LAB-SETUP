<h1 align="center">🛡️ Kali Linux Attack Lab – Week 1 Setup</h1>

<p align="center"><b>A private, isolated environment for learning offensive security the safe way</b></p>

<p align="center">
  <img src="https://img.shields.io/badge/Networkwalks-Internship-critical" alt="Networkwalks">
  <img src="https://img.shields.io/badge/Week-1-blueviolet" alt="Week 1">
  <img src="https://img.shields.io/badge/Hypervisor-VMware-607078" alt="VMware">
  <img src="https://img.shields.io/badge/OS-Kali%20Linux-268BEE" alt="Kali Linux">
  <img src="https://img.shields.io/badge/Lab%20Net-10.0.0.0%2F24-0A66C2" alt="Lab network">
  <img src="https://img.shields.io/badge/Status-Completed-brightgreen" alt="Completed">
  <img src="https://img.shields.io/badge/Docs-PDF%20Report-orange" alt="PDF report">
  <img src="https://img.shields.io/badge/By-Muhamad%20Yoga%20Eka%20Saputra-success" alt="Author">
</p>

---

## 🧭 About This Project

This repository documents my Week 1 project in the Networkwalks Cybersecurity Internship: preparing a lab where Kali Linux plays the role of the attacker. I built it together with my lab partner on her Windows laptop with VMware Workstation. The lab keeps practice traffic on a private NAT network, so nothing leaks to real networks, while Kali can still download tools and updates from the Internet.

---

## ✅ At a Glance

| Requirement | How it was met | Status |
| --- | --- | --- |
| Hypervisor as the lab base | VMware Workstation (VirtualBox settings mapped one-to-one) | ✅ |
| Attacker machine | Kali Linux VM | ✅ |
| Lab subnet 10.0.0.0/24 | VMnet8 set to 10.0.0.0/24 | ✅ |
| NAT network | VMnet8 in NAT mode | ✅ |
| Clipboard & drag/drop | Guest Isolation enabled | ✅ |
| Shared Downloads folder | Shared Folders → `/mnt/hgfs/Downloads` | ✅ |
| Kali IP 10.0.0.2/24 | Static IP via `nmtui` | ✅ |
| Full Internet access | Gateway 10.0.0.1 + DNS 8.8.8.8, ping tests passed | ✅ |

---

## 📄 Full Report

Every configuration step, with screenshots, is in my PDF report:

<p align="center">
  <a href="WK1-PM1_Lab_Setup_Report_Muhamad_Yoga_Eka_Saputra.pdf"><b>📥 Open the Lab Setup Report (PDF)</b></a>
</p>

What the report walks through:

| # | Step |
| --- | --- |
| 1 | Create the Kali VM in VMware |
| 2 | Move VMnet8 (NAT) to 10.0.0.0/24 |
| 3 | Check the DHCP pool |
| 4 | Set the NAT gateway to 10.0.0.1 |
| 5 | Point the Kali adapter to VMnet8 |
| 6 | Configure Kali's static IP |
| 7 | Enable clipboard & drag-and-drop |
| 8 | Share the host Downloads folder |

---

## 🎯 Goals

- Build a repeatable lab that later weeks can extend
- Keep the attacker machine on its own isolated subnet
- Give Kali a predictable, fixed address
- Make moving files and text between host and VM effortless
- Prove every requirement with a test

---

## 🧪 Why This Lab?

- Practise reconnaissance, scanning and enumeration
- Capture and inspect network traffic
- Test vulnerabilities on lab targets only
- Understand attacks in order to build better defences

> ⚠️ All testing is done on machines we own. No outside system is ever touched.

---

## 🗺️ Network Layout

```
   Internet
      │
  [ VMware NAT gateway · 10.0.0.1 ]      ← Windows host
      │
  VMnet8 (NAT) · 10.0.0.0/24
  ├─ Static range : 10.0.0.2 – 10.0.0.99
  └─ DHCP pool    : 10.0.0.128 – 10.0.0.254
      │
  [ Kali Linux · 10.0.0.2/24 ]           ← attacker machine
```

Phase 2 will add Windows 11/10/7 and Android targets to this same subnet.

---

## 🔧 Environment Details

| Item | Value |
| --- | --- |
| Host | Windows laptop (lab partner's) |
| Hypervisor | VMware Workstation |
| Guest | Kali Linux (ISO install) |
| Resources | 6 GB RAM · 4 vCPU · 40 GB disk |
| Network | VMnet8 (NAT) |
| Kali address | 10.0.0.2/24 |
| Gateway / DNS | 10.0.0.1 / 8.8.8.8 |

**VirtualBox vs VMware – what we used**

| The task said (VirtualBox) | We used (VMware) |
| --- | --- |
| NATNetwork | VMnet8 (NAT) |
| Attach adapter to NATNetwork | Custom: VMnet8 (NAT) |
| Shared Clipboard / Drag'n'Drop | Options → Guest Isolation |
| Shared Folders | Options → Shared Folders |

---

## 🔬 Test Results

| Test | Command | Outcome |
| --- | --- | --- |
| Interface | `ip -br addr` | eth0 UP · 10.0.0.2/24 |
| Routing | `ip route` | default via 10.0.0.1 |
| Gateway | `ping -c 4 10.0.0.1` | ✅ reachable |
| Internet | `ping -c 4 8.8.8.8` | ✅ reachable |
| DNS | `ping -c 4 google.com` | ✅ resolves |
| Shared folder | `ls /mnt/hgfs/Downloads` | ✅ files visible |
| Clipboard | copy-paste host ↔ Kali | ✅ both ways |
| Drag & drop | host file → Kali | ✅ copied |

---

## 🧯 Troubleshooting Log

**Issue 1 – Default gateway sat on Kali's address**
VMware's NAT gateway defaults to `.2`, which the task reserves for Kali.
➜ *Fix:* Virtual Network Editor → VMnet8 → NAT Settings → gateway `10.0.0.1`, then Kali set to `10.0.0.2/24` with gateway `10.0.0.1` in `nmtui`.

**Issue 2 – Guide written for VirtualBox, lab built on VMware**
Menu names and options did not match the course screenshots.
➜ *Fix:* mapped each VirtualBox setting to its VMware equivalent before configuring.

**Tip – no Internet after a static IP change (Kali 2026.x)**

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

---

## 📚 Key Takeaways

1. **Concepts over menus** – VirtualBox's NAT Network and VMware's VMnet NAT solve the same problem.
2. **Plan addresses early** – separating static IPs from the DHCP pool prevents clashes later.
3. **Question defaults** – the default gateway conflicted with the design, so the design won.
4. **Integration saves time** – clipboard and shared folders make daily lab work smoother.
5. **Write it down** – documentation turns a one-off setup into something anyone can rebuild.

---

## ⚖️ Ethics Statement

This lab exists purely for learning. Every technique practised here stays inside our own isolated environment and is never used on systems without written permission.

---

## 🔗 Useful Links

- [Kali Linux downloads](https://www.kali.org/get-kali/)
- [VMware Workstation](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion)
- [Networkwalks](https://networkwalks.com)

---

## 🙋 About Me

**Muhamad Yoga Eka Saputra** – Cybersecurity Intern, Networkwalks
[LinkedIn](https://www.linkedin.com/in/muhamad-yoga-eka-saputra-5a4392225) · [GitHub](https://github.com/muhamadyoga2345-ux)

| Program | Batch | Week | Project | Repository |
| --- | --- | --- | --- | --- |
| Networkwalks Cybersecurity Internship | B084 | 1 | WK1-PM1 – Cybersecurity Lab Setup | NETWORKWALKS-YOGA-B084-WK1-PM1-CYBERSECURITY-LAB-SETUP |
