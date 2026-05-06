## **Proxmox Networking: Practical Troubleshooting Guide**

এই গাইডটি আপনার ল্যাব এনভায়রনমেন্টে ফিজিক্যাল ইন্টারফেস, ব্রিজ কনফিগারেশন এবং কানেক্টিভিটি সমস্যা সমাধানের জন্য তৈরি করা হয়েছে।

### **১. Problem Identification (সমস্যা শনাক্তকরণ)**
*   **Physical NIC Status:** সার্ভারের ফিজিক্যাল ইন্টারফেস (যেমন `nic0`) 'DOWN' অবস্থায় থাকা, যার ফলে কোনো ডেটা আদান-প্রদান সম্ভব হয় না।
*   **Destination Host Unreachable:** ফিজিক্যাল লিঙ্ক ডাউন থাকায় গেটওয়ে বা ইন্টারনেটে (যেমন `8.8.8.8`) পৌঁছাতে না পারা।
*   **DNS Failure:** নামের রেজোলিউশন না হওয়া (`google.com` পিং না হওয়া)।
*   **Bridge Misconfiguration:** প্রোফাইলে `bridge-ports none` দেওয়া থাকা, ফলে ফিজিক্যাল পোর্টের সাথে ব্রিজের কোনো সংযোগ থাকে না।

### **২. Troubleshooting Steps & Solutions**

| Step | Action | Command/Check | Explanation |
| :--- | :--- | :--- | :--- |
| **১** | **Check NIC Status** | `ip a` | ইন্টারফেস 'UP' না 'DOWN' তা যাচাই করা। |
| **২** | **Manual Link Up** | `ip link set nic0 up` | সফটওয়্যার লেভেল থেকে ইন্টারফেসটিকে সচল করা। |
| **৩** | **Verify Bridge Ports** | `cat /etc/network/interfaces` | ব্রিজটি সঠিক ফিজিক্যাল পোর্টের সাথে যুক্ত কি না তা দেখা। |
| **৪** | **Fix Bridge Mapping** | `bridge-ports nic0` | `none` এর পরিবর্তে সঠিক পোর্ট বসিয়ে ব্রিজ ও ফিজিক্যাল লিঙ্ক যুক্ত করা। |
| **৫** | **Apply Changes** | `systemctl restart networking` | নতুন কনফিগারেশন কার্যকর করার জন্য নেটওয়ার্ক সার্ভিস রিস্টার্ট দেওয়া। |

### **৩. Key Technical Concept (The Logic)**
নেটওয়ার্কিংয়ে একটি **Bridge (vmbr0)** একটি ভার্চুয়াল সুইচের মতো কাজ করে। যদি এই সুইচে কোনো ফিজিক্যাল ক্যাবল (Physical NIC) যুক্ত না থাকে (`bridge-ports none`), তবে ভার্চুয়াল মেশিনগুলো বাইরের দুনিয়ার সাথে যোগাযোগ করতে পারে না। আবার আপনার হাতের লেখা নোট অনুযায়ী, **Bonding** ব্যবহার করলে ব্রিজের পোর্টে সরাসরি `nic0` এর বদলে বন্ড ইন্টারফেসটি (`bond0`) উল্লেখ করতে হয়।

---

