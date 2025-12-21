# OSTEP introduction
**virtualization of CPU**

To achieve the purpose of easy to use system, OS uses a technique: Virtualization. OS takes a physical resource and transform it into vertual
form of itself. Therefore a OS can also be called virtual machine. OS exposes system calls, API,orstandard library for application to use to run program, access memory ... Sometimes
Turning a single CPU into seemingly infinite number of CPUS and allow many program seemingly to run at once is **virtualizing the CPU**. 
Hence the role of OS is a resource manager

**virtualization of memory**

Model of physical memory is simply an array of bytes. To read or write, applications specify the data and address. Memory is accessed all the time when
program is running. They store data structure, instructions all in memory. Each process has its own memory address. OS maps to
physical address.

**concurrency**

**persistence**
Volatile storage loses memory when poweroff, so we need persistent storage. Hard drive, SSD are commnon storage devices.
File system manages the disk. Files are shared across processes. System calls like open() write() close() are routed to file sysetm
which handles the requests.

**design goals**
build abstractions. high performance, minimize overhead. protection, isolation is key part of protecting process from other bad process.

# The process
<details>
A process is a running program. One often wants to run many programs. OS virtualizes CPU by running one process, stop it and switch to another one.
This technique is also known as time sharing allowing users to run many concurrent processes. The tradeoff is performance. To fulfill virtualization
we need some low level mechanisms, and high leven intelligence. Context switch allows OS to stop one program and run another. This is also 
called time sharing. Hight level intelligence is embodies in policies. Policy algorithm that make decision within the OS.

**abstraction of process**
Process is an abstraction of a running program provided by the OS.

- machine state (what a program can read update, what machine parts are needed):
  - one component of machine state is memory. The memory a process can address is part of the process.
  - registers. eg program counter(PC), stack pointer, frame pointer
  - persistent storage
    
**process creation**
1. load its code and any static data into memory/ address space of the process. Program originally reside on disk in executable format
   OS read bytes from disk and place them in memory.
2. loading process was done eagerly what all at once before program running. Now it is lazily, only loaded code that are needed to use (consider
3. paging and swapping).
4. allocating run-time stack/ stack, OS allocates stack and give it to process. initialize stack with argument eg fill in argc and argv
5. allocate heap use malloc()
6. IO setup
7. start running at main(), OS transfer control to the process

**process state**
1. running: executing instructions
2. ready: ready but OS chooses not to run
3. blocked: wait for other events eg IO
move from ready to running means the process has been schdules, otherwise deschedules. To run or stop which process is done by OS scheduler.

**data structures**
OS needs data structure to track indormation on processes, their states. Process list: a process list of processes, information about all 
process in a system. Each entry is called process control block PCB or process descriptor, information about a process.
</details>

# mechanism: limited direct execution

Motivation: how to virtualize cpu without too much overhead and with control over process.

Solution: limited direct execution

Direct execution on CPU is fast but process may abuses a system. OS doesn't want to give complete control to the process, but a process still need to be able to perform IO and some
restricted operations.

the approach we take is **user mode**: code in user mode is restricted what it can do. eg cant issue io request. program do not have full access to hardware resources
in contract is **kernel mode** : OS runs in kernel mode, code can executing privileged instrictions eg IO requests, OS has full access machine resources.
for user process to perform privileged operation, user programs perform a **system call** 

to execute a system call, a program must executes **trap** instruction. this instruction jumps into the kernel and raises privilege level to kernel mode. The
system then can perfom privileged operations. When finishes, OS call **return from trap** return to user program and reduce privilege back to user mode. Hardware
needs to remember where to return back. Put some registers onto **kernel stack** and pop after finishing. How does trap know which code to run? Kernel
does this by setting a **trap table**. This tell hardware what code to run when a something happens. OS tells the harsware locations of the trap handlers, so 
hardware know hat code to jump to when system calls and other exeptions happen. **A system call number** is assigned to each system call to make it exact. 
User code needs to place system call number in register, or any location on the stack. The OS when encountering inside trap hanler, examine the number and execute code.
Actually, user code only request a service via number. Tell hardware where trap tables are is privileged operation.

**switch between processes**

When program is running on cpu, os is not, how os take control? in **cooperative approach** OS may regain contro by waiting for a system call or illegal operation.
**a non-cooperative approach** A **time interrupt**, a time devide raises interrupt every many milliseconds, running process is haltes, interrupt handler in OS
runs. Hardware also has to remember to save state of the program eg registers, pc... onto kernel stack.

To decide which process to run next is done by **scheduler**. **Context switch** all the os to do is saving register values. os execute low level assembly
code to save general registers, pc, kernel stack pointer. When time inerrupt happens during A's execution.
cpu switch to from user mode to kernel mode, to A kernel stack, save user registers to kernel stack, done by software. A is in kernel.
Now in kernel mode and A's execution state, OS saves these into A PCB. Loads B's saved registers from PCB, switch to B kernel stack, restore execution. It's like
trapped into kernel B.

Overall, let program run whatever it want to run, but set up limit in hardware.
