# Day 02 – Understanding Linux Internals

---

## Core Components of Linux

Linux is mainly divided into three important components:

### 1. Kernel
The **Linux Kernel** is the core component of the operating system. It acts as a bridge between the **hardware and software**.

Key responsibilities of the kernel include:

- Managing **CPU and memory**
- Handling **process scheduling**
- Managing **hardware devices** (disk, network, etc.)
- Controlling **system calls between applications and hardware**

In simple terms, the kernel ensures that **software programs can communicate with the underlying hardware safely and efficiently**.

---

### 2. User Space
**User Space** is the environment where user applications and system programs run.

Examples include:

- Shells like **Bash**
- System utilities like `ls`, `cp`, `top`
- Applications like **Nginx, Docker, databases, etc.**

Programs running in user space **cannot directly access hardware**.  
Instead, they interact with the kernel using **system calls**.

This separation helps improve **system stability and security**.

---

### 3. Init / systemd
When a Linux system boots, the **kernel is the first component that loads**.  
After the kernel initializes the system, it starts the **init system**.

The init system is responsible for **starting and managing all other system services**.

Most modern Linux distributions use **systemd** as the default init system.

---

## How Processes Are Created and Managed

A **process** is simply a running instance of a program.

### Process Creation
Processes in Linux are usually created using a mechanism called **fork()**.

The steps are:

1. A parent process creates a copy of itself using **fork()**.
2. The new process is called a **child process**.
3. The child process may then run a different program using **exec()**.

---

### Process Management

Linux manages processes using a **scheduler** inside the kernel.

The scheduler decides:

- Which process should use the CPU
- How long a process can run
- When a process should pause

Each process has a unique **Process ID (PID)**.

Common commands used to inspect processes:

```bash
ps
top
htop
kill
