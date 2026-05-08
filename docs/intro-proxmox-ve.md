Proxmox VE কী?

Proxmox Virtual Environment (সংক্ষেপে Proxmox VE বা PVE) হলো একটি ওপেন-সোর্স সার্ভার ভার্চুয়ালাইজেশন প্ল্যাটফর্ম। এটি ব্যবহার করে একটি ফিজিক্যাল সার্ভারকে ভাগ করে একাধিক ভার্চুয়াল মেশিন (VM) এবং কন্টেইনার চালানো যায়।

সহজভাবে বললে, একটি শক্তিশালী কম্পিউটারের ভেতরে অনেকগুলো আলাদা “ভার্চুয়াল কম্পিউটার” তৈরি করার সিস্টেমই হলো Proxmox।

Proxmox কেন ব্যবহার করা হয়?
১. Virtualization (KVM)

Proxmox এর মূল ভার্চুয়ালাইজেশন প্রযুক্তি হলো:

KVM (Kernel-based Virtual Machine)

এটি ব্যবহার করে একই হার্ডওয়্যারে একসাথে:

Ubuntu
Windows Server
Kali Linux
Debian
CentOS

এর মতো একাধিক অপারেটিং সিস্টেম চালানো যায়।

উদাহরণ:

একটি PC তে আপনি একসাথে চালাতে পারেন:

VM Name	OS
Web-Server	Ubuntu
Database-Server	Debian
Windows-Lab	Windows 11
Security-Test	Kali Linux
