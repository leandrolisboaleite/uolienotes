# Kernel Mode

All code that runs in kernel mode shares a single [virtual address space](https://learn.microsoft.com/en-us/windows-hardware/drivers/gettingstarted/virtual-address-spaces). Therefore, a kernel-mode driver isn't isolated from other drivers and the operating system itself. If a kernel-mode driver accidentally writes to the wrong virtual address, data that belongs to the operating system or another driver could be compromised. If a kernel-mode driver crashes, the entire operating system crashes.

This diagram illustrates communication between user-mode and kernel-mode components.

<figure><img src="../../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
