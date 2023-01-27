---
description: Session Manager Subsystem also known as the Windows Session Manager
---

# smss.exe

is responsible for creating new sessions. It is the first user-mode process started by the kernel.

This process starts the kernel and user modes of the Windows subsystem (you can read more about the NT Architecture [here](https://en.wikipedia.org/wiki/Architecture\_of\_Windows\_NT)). This subsystem includes win32k.sys (kernel mode), winsrv.dll (user mode), and csrss.exe (user mode).&#x20;

\
**Smss.exe** starts **csrss.exe** (Windows subsystem) and **wininit.exe** in Session 0, an isolated Windows session for the operating system, and **csrss.exe** and **winlogon.exe** for Session 1, which is the user session. The first child instance creates child instances in new sessions, done by smss.exe copying itself into the new session and self-terminating. You can read more about this process [here](https://en.wikipedia.org/wiki/Session\_Manager\_Subsystem).

Session 0 (csrss.exe & wininit.exe)

<figure><img src="../../../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

Any other subsystem listed in the **`Required` ** value of **`HKLM\System\CurrentControlSet\Control\Session Manager\Subsystems`** is also launched.

<figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

SMSS is also responsible for creating environment variables, virtual memory paging files and starts winlogon.exe (the Windows Logon Manager).

### Normal Behaviour

<figure><img src="../../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

**Image Path:** %SystemRoot%\System32\smss.exe&#x20;

**Parent Process:** System&#x20;

**Number of Instances:** One master instance and child instance per session. The child instance exits after creating the session.&#x20;

**User Account:** Local System

&#x20;**Start Time:** Within seconds of boot time for the master instance

### Unusual behaviour

* A different parent process other than System (4)
* The image path is different from C:\Windows\System32
* More than one running process. (children self-terminate and exit after each new session)
* The running User is not the SYSTEM user
* Unexpected registry entries for Subsystem
