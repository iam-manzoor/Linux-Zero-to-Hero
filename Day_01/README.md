## Core Components of Linux
Linux is structured in layers, from hardware to user applications. Here's a high-level overview:

```
+----------------------------------------------------+
| User Applications (Vim, Docker, Apache, etc.)     |
+----------------------------------------------------+
| Shell (Bash, Zsh, Fish, etc.)                     |  <-- Part of the OS
+----------------------------------------------------+
| System Libraries (glibc, libc, OpenSSL, etc.)     |  <-- Part of the OS
+----------------------------------------------------+
| System Utilities (ls, grep, systemctl, etc.)      |  <-- Part of the OS
+----------------------------------------------------+
| Linux Kernel (Process, Memory, FS, Network)       |  <-- Core of the OS
+----------------------------------------------------+
| Hardware (CPU, RAM, Disk, Network, Peripherals)   |
+----------------------------------------------------+
```
### (1) Hardware Layer.
- Hardware layer consists of physical components such as CPU, RAM, Network interfaces, Disk, etc..
- The OS interacts with hardware via device drivers in the kernel

### (2) Kernel - The core of the Linux OS
- **Manages system resources directly:**
  - **Memory Management:** The kernel tracks, allocates, and deallocates system memory, ensuring that each process gets access without interfering with others.
  - **Process Management:** Manage processes, controlling which programs get CPU for how long, enabling multitasking.
  - **Device Management:** Device drivers in the kernel provide a unified interface for hardware devices, translating software requests into hardware actions.
  - **File System Management:** Handles storage, retrieval, and Permissions. Also support multiple FS.
  - **Network Management:** Manages network interface and protocols, allowing communication between networks.
  - **System Calls and Security:** Application requests kernel service via system calls, and the kernel enforces permissions and security policies.
- **Kernel Space VS User Space:**
  - **Kernel Space:** Protected memory area where the core part of the kernel, device drivers and related extensions run.
- **Kernel Version:**
- **Monolithic Kernel:**
