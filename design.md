---
layout: page
title: Charlotte OS Design Document
# subtitle: Kitty go purrrrrrrrrrr!
---

## General

- Design Goals
	- Ease of use for nontechnical users
	- Ease of troubleshooting for both technical and nontechnical users
	- Ease of developing applications
	- Providing an erognomic environment for developers
	- Making the system itself easy to hack on
		- Ensure all OS code is well organized and well documented
- MPL 2.0 License used when possible
- Include features that each set of target users care about
- Provide a modern set of user interfaces
	- Create a good looking and well optimized native Rust UI
		- Widget based GUI inspired by KDE Plasma
	- Create a modern and easy to use text shell
		- More feature rich API than curses and conio
		- Potentially allow mouse usage

## Kernel - Charlotte Core

#### Overview: The kernel will provide mechanisms to make use of hardware resources and it will enforce the policies set by privileged userspace software.

- Hybrid Kernel
	- Kernel extensions run as privileged processes in userspace
	- Drivers can be compiled into the kernel or run as kernel extensions
- Memory Management
	- MMU required
	- Physical Memory Manager
		- Tracks used and unused page frames
		- Tracks number of page frames that may be needed but have not yet been allocated
	- Virtual Menmory Manager
		- An interface provided by the arch module in the kernel to abstract over ISA specific page map management operations
		- Operations
			- Create a new page map
			- Delete an existing page map
			- Add mappings to an existing page map
			- Remove mappings from an existing page map
			- Set or clear pages' present bits
			- Change MMU permissions on one or more page(s) in a given page map
		- Given the number of ISA specific operations involved this subsystem is best implemented in arch on a per ISA basis
		and abstracted to a common interface.
- Thread Scheduling
	- TBD
- ACPI
	- Static tables are parsed using custom code
	- AML interpretation will be performed using Rust bindings to LAI, the Lightweight AML Interprer
- System Call Interface
	- The system call interface will use a `target-action-arguments` pattern for extensibility and ease of use
		- `target`: Either an OS subsystem or capability
		- `action`: The requested action for the kernel to perform
		- `arguments`: Any other information needed to perform the requested action
- Kernel Bus (kbus)
	- Intercontext communication mechanism backed by kernel shared memory
	- Provides full duplex communication channels
	- Capabale of unicast and multicast communication
	- Broadcast is not allowed for performance and security reasons
	- Provides the only way to send capabilities between contexts

## Executive Service - Charlotte Exec

#### Overview: The executive service is the primary userspace portion of the operating system. It controls all system configuration, handles process management, user management and implements the configured security policy. It is also responsible for starting and stopping all other system services including kernel extension services.
- Is given all possible capabilities at system startup and has the responsibility of providing or denying processes capabilities upon request.
- Manages all system services and kernel extension services.
- Creates and launches all new processes by default.
- Can operate as an OOM killer if configured to do so.
- Makes all necessary privileged system calls to configure the kernel to enforce system policies
- Reads the configuration changes automatically from the namespace and implements the requested changes
- Tracks processes and their resources (the kernel has no concept of process at all)
- Handles all user management
- Enforces security policy by withholding capabilities when appropriate.
- Assigns ownership of global resources like the framebuffer to the services configured to manage them

## Namespace

#### Overview: The system namespace is a heirarchical directory of entries that represent a variety of differnt things in the system. It is used to enumerate and interact with almost all system resources and components.

- Implemented by the namespace service which should be a direct child of the executive service
- Paths in the namespace are URIs with the following format `sns://addr/path`
	- `sns`: The subnamespace to be accessessed
	- `addr`: Some means of identifying the host on which to look for the given path in the given subnamespace. If omitted, localhost is assumed.
	- `path`: The path the to the object or directory being referenced
- The filesystem is a subnamespace that is implemented by the OS itself.
- Processes with the appropriate permissions can create and mount their own subnamespaces into the system namespace

## User Interface

#### Overview: The system will provide two user interfaces, a text shell and a graphical UI for users to interact with it.

- Text UI
	- A rich TUI that allows users to interact with the system.
	- The programming interface for the TUI should be richer than curses or conio and potentially allow a mouse to be used
- Graphical UI
	- A widget based UI modelled after the KDE Plasma Desktop but made perhaps made more multitouch friendly
