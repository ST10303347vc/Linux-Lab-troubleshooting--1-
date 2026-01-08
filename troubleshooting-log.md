# Linux Troubleshooting Lab: DNS Failure & Recovery

## 📝 Project Overview
This lab simulates a common production issue: a Linux server that has lost internet connectivity due to DNS misconfiguration. The goal was to diagnose the issue, identify why standard repairs were failing, and restore connectivity.

## 🛠️ Environment
* **Platform:** AWS EC2
* **OS:** Ubuntu 24.04 LTS
* **Instance Type:** t2.micro

## 🔍 Phase 1: Symptom Analysis
The server reported a "Temporary failure in name resolution" when attempting to reach external domains.

1. **Connectivity Check:**
   
   - `ping google.com`: **FAILED** (Confirming a DNS/Name Resolution issue).
   - `ping 8.8.8.8`: **SUCCESS** (Outbound IP connectivity is functional)

2. **Configuration Review:**
   - Ran `cat /etc/resolv.conf`.
   - **Finding:** The nameserver was set to `192.0.2.0`, which is a non-existent/invalid DNS server.

## 🚧 Phase 2: The "Twist" (The Roadblock)
When attempting to fix the file using `sudo nano /etc/resolv.conf`, the system refused to save changes, even as the root user.

1. **Initial Hypothesis:** I suspected the "Immutable Bit" (`chattr +i`) was set on the file.
2. **Investigation:** Ran `lsattr /etc/resolv.conf`.
3. **Observation:** Received an error: `lsattr: Operation not supported while reading flags`.
4. **Root Cause Discovery:** Ran `ls -l /etc/resolv.conf` and found the file was a **Symbolic Link** to `../run/systemd/resolve/stub-resolv.conf`. 
   - Files in `/run` are stored in memory (tmpfs) and do not support `chattr` attributes. 
   - The DNS was being managed by `systemd-resolved`, creating a conflict with manual edits.



## ✅ Phase 3: Resolution
To bypass the broken symbolic link and restore immediate access:

1. **Removed the broken link:** `sudo rm /etc/resolv.conf`
2. **Created a static configuration:** `sudo sh -c 'echo "nameserver 8.8.8.8" > /etc/resolv.conf'`
3. **Verification:**
   - `ping -c 4 google.com`: **SUCCESS** (4 packets transmitted, 4 received).

## 💡 Lessons Learned
- DNS issues can mimic total network outages; always test via IP address first.
- In modern Linux (Ubuntu 22.04+), `/etc/resolv.conf` is often a symbolic link. Standard file commands may behave differently.
- Deleting and recreating a configuration file is sometimes more efficient than trying to repair a complex symbolic link chain in an emergency.
