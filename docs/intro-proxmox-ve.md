# Proxmox VE কী?

Proxmox Virtual Environment (সংক্ষেপে **Proxmox VE** বা **PVE**) হলো একটি ওপেন-সোর্স সার্ভার ভার্চুয়ালাইজেশন প্ল্যাটফর্ম। এটি ব্যবহার করে একটি ফিজিক্যাল সার্ভারকে ভাগ করে একাধিক ভার্চুয়াল মেশিন (VM) এবং কন্টেইনার চালানো যায়।

সহজভাবে বললে, একটি শক্তিশালী কম্পিউটারের ভেতরে অনেকগুলো আলাদা “ভার্চুয়াল কম্পিউটার” তৈরি করার সিস্টেমই হলো Proxmox।

---

# Proxmox কেন ব্যবহার করা হয়?

## ১. Virtualization (KVM)

Proxmox এর মূল ভার্চুয়ালাইজেশন প্রযুক্তি হলো:

* KVM (Kernel-based Virtual Machine)

এটি ব্যবহার করে একই হার্ডওয়্যারে একসাথে:

* Ubuntu
* Windows Server
* Kali Linux
* Debian
* CentOS

এর মতো একাধিক অপারেটিং সিস্টেম চালানো যায়।

### উদাহরণ:

একটি PC তে আপনি একসাথে চালাতে পারেন:

| VM Name         | OS         |
| --------------- | ---------- |
| Web-Server      | Ubuntu     |
| Database-Server | Debian     |
| Windows-Lab     | Windows 11 |
| Security-Test   | Kali Linux |

---

## ২. Containerization (LXC)

Proxmox এ LXC Container সাপোর্ট রয়েছে।

Container হলো VM এর চেয়ে অনেক হালকা ও দ্রুত।

### ব্যবহার:

* Docker Host
* Web App
* API Server
* Monitoring Service

### সুবিধা:

* কম RAM লাগে
* দ্রুত চালু হয়
* CPU কম ব্যবহার করে

---

## ৩. Type-1 Hypervisor

Proxmox সরাসরি হার্ডওয়্যারের ওপর ইনস্টল হয় (Bare Metal)।

অর্থাৎ:

Hardware → Proxmox → VM/Container

এ কারণে পারফরম্যান্স অনেক ভালো হয়।

---

# Proxmox Web GUI Access

Proxmox এর GUI ওপেন করতে শুধু IP দিলেই হবে না।

ব্যবহার করতে হবে:

```bash
https://IP_ADDRESS:8006
```

### Example

```bash
https://192.168.253.132:8006
```

কারণ:

* Proxmox HTTPS ব্যবহার করে
* Default Port = 8006

---

# SSL Warning কেন আসে?

ব্রাউজারে অনেক সময় দেখা যায়:

> “Your connection is not private”

এটি আসে কারণ Proxmox self-signed SSL certificate ব্যবহার করে।

সমাধান:

* Advanced এ ক্লিক করুন
* Proceed এ চাপুন

---

# Proxmox Network Configuration

নেটওয়ার্ক পরিবর্তনের জন্য:

```bash
nano /etc/network/interfaces
```

---

# Example Network Configuration

```bash
iface vmbr0 inet static
    address 192.168.68.100/24
    gateway 192.168.68.1
    bridge-ports ens33
    bridge-stp off
    bridge-fd 0
```

## এখানে কী বোঝায়?

| Line           | Meaning           |
| -------------- | ----------------- |
| address        | Proxmox Server IP |
| gateway        | Router IP         |
| bridge-ports   | Physical NIC      |
| bridge-stp off | STP বন্ধ          |
| bridge-fd 0    | Forward delay     |

---

# Bridged Network কী?

Bridged Mode এ VM গুলো সরাসরি আপনার মূল নেটওয়ার্কে যুক্ত হয়।

### Example

যদি আপনার Wi-Fi Router এর নেটওয়ার্ক হয়:

```text
192.168.68.x
```

তাহলে VM গুলোও ঐ রেঞ্জের IP পাবে।

---

# VMware থেকে Bridged Mode Enable

## Steps

### Step 1: Settings

* VM এর ওপর Right Click
* Settings এ যান

### Step 2: Network Adapter

* Network Adapter নির্বাচন করুন
* Bridged Mode Select করুন
* Wi-Fi Adapter Select করুন

### Step 3: Reboot

```bash
reboot
```

---

# SSH দিয়ে Proxmox Control

SSH ব্যবহার করে Remote Login করা যায়।

```bash
ssh root@192.168.253.133
```

---

# Proxmox Cluster কী?

Cluster হলো একাধিক Proxmox Server (Node) কে একসাথে যুক্ত করা।

ধরুন আপনার ৩টি Server আছে:

| Node   | Role         |
| ------ | ------------ |
| pve-01 | Main Node    |
| pve-02 | Backup Node  |
| pve-03 | Compute Node |

এগুলোকে Cluster করলে:

* এক GUI থেকে সব Server Control
* VM Migration
* High Availability
* Shared Storage
* Load Balancing

সব সম্ভব হয়।

---

# Cluster Architecture Example

```text
               ┌──────────────┐
               │ Proxmox GUI  │
               └──────┬───────┘
                      │
     ┌────────────────────────────────┐
     │            Cluster             │
     └────────────────────────────────┘
          │            │            │
     ┌────▼───┐   ┌────▼───┐   ┌────▼───┐
     │ pve-01 │   │ pve-02 │   │ pve-03 │
     └────────┘   └────────┘   └────────┘
```

---

# Cluster এর সুবিধা

## ১. Centralized Management

একটি Browser থেকেই সব Node Manage করা যায়।

---

## ২. Live Migration

Running VM বন্ধ না করেই অন্য Node এ নেওয়া যায়।

### Example

```text
pve-01 → pve-02
```

---

## ৩. High Availability (HA)

যদি একটি Server নষ্ট হয়:

* VM Automatically অন্য Node এ Start হবে।

এতে Downtime কমে যায়।

---

## ৪. Shared Storage

Cluster এ ব্যবহার করা যায়:

* ZFS
* Ceph
* NFS

ফলে সব Node একই Storage Access করতে পারে।

---

# Quorum কী?

Cluster এ Quorum হলো Voting System।

এটি নিশ্চিত করে:

* কোন Node Active
* কোন Node Failure
* Split-Brain Problem এড়ানো

---

# কেন ৩টি Node দরকার?

২টি Node হলে সমস্যা:

```text
Node-1 thinks Node-2 is dead
Node-2 thinks Node-1 is dead
```

এটিকে বলে:

## Split Brain

কিন্তু ৩টি Node থাকলে Majority Vote সম্ভব হয়।

---

# Real-Life Example

একটি ছোট কোম্পানির Setup:

| Server | কাজ         |
| ------ | ----------- |
| pve-01 | Web Hosting |
| pve-02 | Database    |
| pve-03 | Backup + HA |

যদি pve-01 নষ্ট হয়:

* VM Automatically pve-02 তে চলে যাবে।

---

# Basic Cluster Commands

## Create Cluster

```bash
pvecm create mycluster
```

## Join Cluster

```bash
pvecm add 192.168.68.100
```

## Check Status

```bash
pvecm status
```

---

# Proxmox কোথায় বেশি ব্যবহার হয়?

* Data Center
* Cloud Lab
* Cyber Security Lab
* Home Lab
* University Research
* Enterprise Infrastructure
* DevOps Environment

---

