---
layout: page
title: CharlotteOS Design Document
---

# CharlotteOS Design Document

## General

### Design Goals
- Ensure ease of use for non-technical users.
- Facilitate easy troubleshooting for both technical and non-technical users.
- Simplify the development of applications.
- Provide an ergonomic environment for developers.
- Make the system straightforward to modify (hackability).
  - Ensure all OS code is well-organized and well-documented.
- Use the GPL 3.0 License wherever possible.
- Include features valued by different target user groups.
- Deliver a modern set of user interfaces.
  - Develop a visually appealing and well-optimized native Rust UI.
    - Widget-based GUI inspired by KDE Plasma.

## Kernel - Charlotte Core

### Overview
The kernel facilitates access to hardware resources and enforces policies set by privileged user-space software.

#### Modular Monolithic Kernel
- Drivers and kernel modules must use the same compiler version as the kernel they target.
- Each kernel release will explicitly specify the compiler version used for its build, ensuring that all modules are compatible.
- Charlotte Core does not guarantee internal stability; module developers are responsible for ensuring compatibility with new kernel releases.
- If module authors wish to use languages other than Rust, they must provide their own C FFI wrapper module to serve as an interface. This wrapper ensures that the module can communicate with the kernel while using the language of the author’s choice.
- The package manager will facilitate easier integration and management of modules, streamlining updates and compatibility checks.

#### Memory Management
- MMU Required by target system
- **Physical Memory Manager**
  - Tracks used and unused page frames using a simple bitmap.
  - Tracks the number of frames that may be needed later for CoW and demand paging.
- **Virtual Memory Manager**
  - Provided by the arch module to abstract ISA-specific page map management operations.
  - Operations include creating and deleting page maps, adding/removing mappings, and managing MMU permissions and other settings on pages.
  - On the x86-64 ISA, this includes handling MTRRs and PATs

#### Thread Scheduling
- Single scheduler tunable for either latency or throughput.
- Uses a preemptive form of Dynamic Quantum Round Robin as the primary scheduling algorithm to achieve weighted fair scheduling.

#### ACPI
- Parse static tables using custom code.
- Integrates Rust bindings to uACPI for AML interpretation.

#### System Call Interface
- Employs a target-action-arguments pattern for extensibility and ease of use.
  - Target: An OS subsystem or capability.
  - Action: The requested action for the kernel to perform.
  - Arguments: Additional information needed to execute the action.

#### Kernel Bus (kbus)
- Handles interprocess communication.
- Provides full duplex communication channels capable of unicast and multicast (broadcasting is prohibited).
- Facilitates capability transfer between contexts and supports both local (via shared memory buffers) and remote (using WebSockets) communications.

## Executive Service - Charlotte Exec

### Overview
The primary user-space portion of the OS, handling system configuration, process and user management, and security policy enforcement.

- Can grants or revoke any capability.
- Manages all system services and acts as an OOM killer when configured to do so.
- Automatically reads and implements configuration changes.
- Enforces security policy by managing capability access.
- Assigns ownership of global resources to designated management services (e.g., framebuffer to the compositor).

## Namespace

### Overview
A hierarchical structure used to enumerate and interact with nearly all system resources and components.

- Managed by the namespace subsystem of the kernel.
- Paths in the namespace are formatted as `sns://host/path`.
  - **sns**: Subnamspace, a section within the namespace that serves a particular pu
  - **Host**: Identifier for the host; defaults to localhost if omitted.
  - **Path**: The specific object or directory referenced.
- The filesystem is a subnamespace
  - The CharlotteOS filesystem will be a custom single rooted filesystem with support for multi-user ownership semantics, file descriptor capabilities, journaling, snapshots, compression, and softwre RAID.
  - The OS will also fully support the exFAT filesystem for use with removable storage media.

## User Interface

### Overview
The primary user interaction with the system is through a graphical shell.

- CharlotteOS will be a GUI first operating system
  - Widget-based UI modeled after the KDE Plasma Desktop.
  - Includes robust support for remote desktop functionality using a secure protocol.
- Terminal mode will be supported for environments where a GUI is not desirable or possible.
  - Can be shown on a display or made available via serial or SSH over a network

## Command Shell
- Provides an easy to use but also scriptable command shell
- Shell scripts inspired by assembly code with each command to be esecuted on a separate line with its operands
  - Control flow will be possible via branch commands
  - This will make it easy to generate shell scripts on the fly
