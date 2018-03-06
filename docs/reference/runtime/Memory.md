<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Memory

This is a basic overview of the memory model in Mbed OS.

```
+---------------------+   Last address of RAM
| Scheduler/ISR stack |
+---------------------+
|          ^          |
|          |          |
|                     |
|      Heap cont.     |
|---------------------|
| User thread n stack |
|---------------------|
| User thread 2 stack |
|---------------------|
| User thread 1 stack |
|---------------------|
|          ^          |
|          |          |
|                     |
|        Heap         |
+---------------------+
|                     |
| ZI: Global data     |
|                     |
+---------------------+
| ZI: Idle stack      |
+---------------------+
| ZI: Timer stack     |
+---------------------+
| ZI: Main stack      |
+---------------------+
|                     |
| ZI: Global data     |
|                     |
+---------------------+
| RW: Vector table    |
+=====================+   First address of RAM
|                     |   Last address of flash
|                     |
|     Application     |
|                     |
|                     |
+---------------------+
|                     |
| Optional bootloader |
|                     |
+---------------------+
| RO: Vector table    |
+---------------------+   First address of flash

```

There are at least two kinds of memory in the system: flash and RAM.

### RAM

Inside RAM, we can distinguish two logical types: static and dynamic memory. The system uses each of them in different ways:

- Static:
    - Vector table (read/write).
    - Global data.
    - Static data.
    - Stacks for default threads (main, timer, idle, scheduler/ISR).
- Dynamic:
    - Heap (dynamic data).
    - Stacks for user threads. Mbed OS will dynamically allocate memory on heap for user thread's stacks.

Stack checking is turned on for all threads, and the kernel errors if it detects an overflow condition.

### Flash

Flash is a read only memory (ROM) that contains:

- Vector table (read only).
- Application code.
- Application data.
- Optional bootloader.
