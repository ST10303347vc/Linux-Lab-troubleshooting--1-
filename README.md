# 🛠️ Linux Lab: Troubleshooting Cloud Network Connectivity

## 📌 Project Overview
In this lab, I provisioned an Ubuntu 24.04 server on AWS and performed a structured "break-and-fix" scenario. I simulated a DNS failure, diagnosed the root cause using Linux networking tools, and implemented a permanent resolution.

## 🎯 Objectives
* Provision and manage **AWS EC2** instances.
* Troubleshoot **OSI Layer 3 (Network)** and **Layer 7 (Application/DNS)** issues.
* Understand **Linux File System Attributes** and **Symbolic Links**.

## 💻 Tech Stack
* **Cloud:** AWS (EC2, Security Groups)
* **OS:** Ubuntu 24.04 LTS
* **Tools:** SSH, ICMP (Ping), Nano, Chattr/Lsattr, Systemd

## 🕵️ The Scenario
The server could communicate via direct IP addresses but failed to resolve domain names (e.g., `google.com`). This prevented system updates and external API communication.



### The "Twist"
While attempting a standard DNS repair, I encountered an `Operation not supported` error. This led to the discovery that `/etc/resolv.conf` was a **Symbolic Link** to a temporary file system (`tmpfs`), which does not support extended file attributes.



## ✅ Key Results
* **Root Cause Identified:** DNS was pointed to a non-existent nameserver, and the configuration file was locked behind a symbolic link chain.
* **Resolution:** Removed the broken link and established a static, valid DNS configuration.
* **Skill Demonstrated:** Ability to pivot strategy when standard tools (like `chattr`) fail due to file system constraints.

## 📂 Detailed Logs
For a step-by-step breakdown of every command used, see the [Troubleshooting Log](./troubleshooting-log.md).
