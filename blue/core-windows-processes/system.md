---
description: The PID for System is always 4
---

# System

The official definition from Windows Internals 6th Edition:

> "_The System process (process ID 4) is the home for a special kind of thread that runs only in kernel mode a kernel-mode system thread. System threads have all the attributes and contexts of regular user-mode threads (such as a hardware context, priority, and so on) but are different in that they run only in kernel-mode executing code loaded in system space, whether that is in Ntoskrnl.exe or in any other loaded device driver. In addition, system threads don't have a user process address space and hence must allocate any dynamic storage from operating system memory heaps, such as a paged or nonpaged pool._"

### &#x20;Normal behavior

<figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

**Image Path**: N/A&#x20;

**Parent Process**: None&#x20;

**Number of Instances**: One

**One User Account**: Local System&#x20;

**Start Time**: At boot time



The information is slightly different if we view the System properties using Process Hacker.

<figure><img src="../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

**Image Path**: C:\Windows\system32\ntoskrnl.exe (NT OS Kernel)&#x20;

**Parent Process:** System Idle Process (0)



### **Unusual behaviour**

* A parent process (aside from System Idle Process (0))
* Multiple instances of System. (Should only be one instance)&#x20;
* A different PID. (Remember that the PID will always be PID 4)
* Not running in Session 0
