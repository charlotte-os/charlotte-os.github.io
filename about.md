---
layout: page
title: About CharlotteOS
---

CharlotteOS is an extremely ambitious novel operating system project. It will be a capability-based, quasi-exokernel OS with paravirtualized hardware interfaces and strong process isolation.

#### CharlotteOS is built around three crucial design points:

- **Capability-based security**: Capabilities are tokens that refer to particular units of a given system resource along with an associated set of permissions. Processes will only be able to perform actions on system resources if they have the capabilities needed to do so.
- **Paravirtualized kernel interfaces**: The kernel, Charlotte Core, will present a set of well-defined, common interfaces for each supported device type which will be mapped by drivers to the equivalent functionality on real hardware, providing fine grained access to hardware functionality and opportunities for software optimization without compromising on security or stability. Higher level abstractions over these low level interfaces will also be available through the use of system libraries.
- **Strongly-typed namespace**: A hierarchical, strongly-typed global namespace will allow for seamless management of system resources and components, making it easy to enumerate and interact with the system. Namespace paths will be URIs which will ease their use and also allow accesses to the namespaces of other hosts without having to mount and unmount directories at all. The filesystem will be part of the namespace with files being one of many types of namespace entries. Unprivileged processes will only be able to access the portions of the namespace that they have the capabilities to see and access.

CharlotteOS will leverage these concepts to be a modern, open-source operating system that exemplifies all of security, performance, flexibility, and ease of use with no compromises. Processes will be heavily sandboxed, with inter-process communication primarily occurring through the kernel bus (kbus), a high-performance communication mechanism for both local and remote communication and the only means by which processes may exchange capabilities.

#### Other tentatively planned features include: 
- Widget-style GUI inspired by KDE Plasma
- Dynamic Quantum Round Robin thread scheduling
- Advanced memory management with processes able to control their own address spaces to a significant degree 
- Modular kernel architecture to support third party extensions and drivers 
- Robust remote desktop capabilities

CharlotteOS plans to offer a unique blend of low-level control for developers and system administrators and ease of use for normal users with the goal of meeting the needs of all of them.

We are actively seeking collaborators who share our vision of building this type of operating system. If you have expertise in OS development or system programming and want to help please join our discord server: [https://discord.gg/XPzJtqFVEe](https://discord.gg/XPzJtqFVEe). Also check out our Github organization to keep up with our progress and possibly help out: [https://github.com/charlotte-os](https://github.com/charlotte-os)
