---
layout: post
title: Ransomware Prevention with FSRM
subtitle:
---

## Ransomware Prevention with FSRM 

These are some ongoing notes for myself I put together on my phone while patching our 2008 R2 servers.  Suffice it to say, it's not meant to be a step by step, but something that I can look back on. Most of what's needed to implement this solution is listed under resources.  I looked into FSRM back in October 2016 after we had a similar incident at the facility I work at.

### What is FSRM?

FSRM actively monitors your Windows Server shares and files and could alert you of any malicious activity you specify.

More so it detects changes in file extensions by applying screen filters, and triggers a system event.  You can use your own choice of monitoring software to report the event.  

### Scheduling Updates

Unfortunately there isn't an easy way to accomplish this, but you'll want to schedule the script to run on a regular basis. You can use something like a Task Scheduler item to accomplish this. Doing this will ensure that your systems are kept up to date with the latest known ransomware file types. I currently have my script set to run daily.  This may seem a bit overkill but it's better safe than sorry, especially in the wake of the WannaCrypt variant.

### Resources 

[Stop CryptoLocker from hitting Windows file shares w/ FSRM](https://chrisreinking.com/stop-cryptolocker-from-hitting-windows-file-shares-with-fsrm/)

[FSRM Experiant CA](https://fsrm.experiant.ca/)

[CryptoBlocker Github](https://github.com/nexxai/CryptoBlocker)

### Important steps before running the PoSh scripts

To ensure you're able to run the powershell scripts, make sure you're on the latest .NET version, and WMI framework.

Restarts

Install Script & Configure Mail
