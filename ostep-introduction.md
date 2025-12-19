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
