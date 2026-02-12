#linux #kernel #hypervisor

[https://www.linux-kvm.org](https://www.linux-kvm.org)

Kernel-based Virtual Machine (KVM) is an open-source virtualization technology built into the Linux kernel that allows the OS to function as a type-1 (bare-metal) hypervisor. Since its integration into the mainline Linux kernel in 2007, it has enabled users to run multiple, isolated virtual machines (VMs) on x86, ARM, PowerPC, and S/390 hardware. KVM leverages hardware virtualization extensions such as Intel VT-x and AMD-V to provide high-performance execution of guest operating systems, including Linux, Windows, and BSD, while benefiting directly from the security, scalability, and resource management features of the underlying Linux kernel.

## Core Architecture

- Hypervisor Type: KVM is a type-1 hypervisor because it is integrated into the kernel, allowing it to manage hardware resources directly without a middle-layer operating system.
- Kernel Modules: The core infrastructure is provided via kvm.ko, with processor-specific modules like kvm-intel.ko or kvm-amd.ko for hardware acceleration.
- VM as a Process: Each virtual machine is treated as a standard Linux process, allowing it to be managed by the native Linux scheduler and security tools.
- QEMU Integration: KVM typically uses QEMU as its userspace component to perform I/O device emulation, such as disks, network cards, and graphics adapters.

## Key Technical Features

- Full Virtualization: Supports running unmodified guest operating systems by providing virtualized BIOS and hardware interfaces.
- Live Migration: Enables the movement of a running virtual machine between physical hosts without service interruption or downtime.
- Memory Management: Inherits Linux memory features, including Non-Uniform Memory Access (NUMA), Kernel Same-page Merging (KSM), and support for huge pages to improve performance.
- Security: Utilizes Security-Enhanced Linux (SELinux) and sVirt to establish mandatory access control (MAC) boundaries between virtual machines and the host.
- Device Performance: Employs the VirtIO API for paravirtualized device drivers, reducing the overhead of emulated I/O for networking and storage.

## Management and Ecosystem

- libvirt: A standard toolkit and API used to manage KVM and other virtualization technologies, providing a stable management layer.
- virsh: A powerful command-line interface for managing virtual machine lifecycles and configurations.
- virt-manager: A graphical desktop application for creating and managing VMs, storage pools, and virtual networks.
- Cloud Integration: Serves as the foundational hypervisor for major cloud platforms and orchestration engines such as OpenStack and Proxmox VE.