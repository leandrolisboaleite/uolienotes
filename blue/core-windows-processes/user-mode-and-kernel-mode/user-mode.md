# User mode

When you start a user-mode application, Windows creates a _process_ for the application. The process provides the application with a private [_virtual address space_](https://learn.microsoft.com/en-us/windows-hardware/drivers/gettingstarted/virtual-address-spaces) and a private _handle table_. Because an application's virtual address space is private, one application can't alter data that belongs to another application. Each application runs in isolation, and if an application crashes, the crash is limited to that one application. Other applications and the operating system aren't affected by the crash.

In addition to being private, the virtual address space of a user-mode application is limited. A process running in user mode can't access virtual addresses that are reserved for the operating system. Limiting the virtual address space of a user-mode application prevents the application from altering, and possibly damaging, critical operating system data.
