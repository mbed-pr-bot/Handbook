<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
<h2 id="rtos-api">RTOS overview</h2>

The Arm Mbed RTOS is a C++ wrapper over the Keil RTX code. For more information about Keil RTX, check <a href="https://github.com/ARM-software/CMSIS/raw/master/CMSIS/Documentation/RTX/CMSIS_RTOS_Tutorial.pdf" target="_blank">the Keil CMSIS-RTOS tutorial</a> and <a href="https://www.element14.com/community/docs/DOC-46650/l/arm-keil-rtx-real-time-operating-system-overview" target="_blank">the element14 introduction to Keil RTX</a>. You can use these resources as a general introduction to RTOS principles; it is important to be familiar with the concepts behind an RTOS in order to understand this guide.

The code of the Mbed RTOS can be found in the <a href="https://github.com/ARMmbed/mbed-os" target="_blank">`mbed-os`</a> repository, in the <a href="https://github.com/ARMmbed/mbed-os/tree/master/rtos" target="_blank">RTOS subdirectory</a>. See <a href="https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/group__rtos.html" target="_blank">the Doxygen</a> for more information.

#### SysTick

System tick timer (SysTick) is a standard timer available on most Cortex-M cores. Its main purpose is to raise an interrupt with set frequency (usually 1ms). You can use it to perform any task in the system, but for platforms using RTOS, including Mbed OS, it provides an interval for the OS for counting the time and scheduling tasks.

Mbed OS uses default SysTick source for most targets, but you can override that using the <a href="http://arm-software.github.io/CMSIS_5/RTOS2/html/group__CMSIS__RTOS__TickAPI.html" target="_blank">Tick API</a> that CMSIS-RTOS2 provides. In which case you'll need to provide your own source of the interrupts.

#### RTOS APIs

The RTOS APIs handle creation and destruction of threads in Arm Mbed OS 5, as well as mechanisms for safe interthread communication. Threads are a core component of Mbed OS 5 (even your `main` function starts in a thread of its own), so understanding how to work with them is an important part of developing applications for Mbed OS 5.

- <a href="/docs/v5.6/reference/thread.html" target="_blank">Thread</a>: The class that allows defining, creating and controlling parallel tasks.
- <a href="/docs/v5.6/reference/mutex.html" target="_blank">Mutex</a>: The class used to synchronize the execution of threads.
- <a href="/docs/v5.6/reference/semaphore.html" target="_blank">Semaphore</a>: The class that manages thread access to a pool of shared resources of a certain type.
- <a href="/docs/v5.6/reference/queue.html" target="_blank">Queue</a>: The class that allows you to queue pointers to data from producer threads to consumer threads.
- <a href="/docs/v5.6/reference/memorypool.html" target="_blank">MemoryPool</a>: This class that you can use to define and manage fixed-size memory pools
- <a href="/docs/v5.6/reference/mail.html" target="_blank">Mail</a>: The API that provides a queue combined with a memory pool for allocating messages.
- <a href="/docs/v5.6/reference/rtostimer.html" target="_blank">RtosTimer</a>: A deprecated class used to control timer functions in the system.
- <a href="/docs/v5.6/reference/eventflags.html" target="_blank">EventFlags</a>: An event channel that provides a generic way of notifying other threads about conditions or events.
- <a href="/docs/v5.6/reference/event.html" target="_blank">Event</a>: The queue to store events, extract them and excute them later.

##### Default timeouts

The Mbed RTOS API has made the choice of defaulting to `0` timeout (no wait) for the producer methods, and `osWaitForever` (infinite wait) for the consumer methods.

A typical scenario for a producer could be a peripheral triggering an interrupt to notify an event; in the corresponding interrupt service routine you cannot wait (this would deadlock the entire system). On the other side, the consumer could be a background thread waiting for events; in this case the desired default behaviour is not using CPU cycles until this event is produced, hence the `osWaitForever`.

<span class="warnings">**Warning**: No wait in ISR <br> When calling an RTOS object method in an ISR, all the timeout parameters must be set to 0 (no wait); waiting in ISR is not allowed. </span>

##### The main() function

The function `main` is a special thread function that is started at system initialization and has the initial priority `osPriorityNormal`; it is the first thread the RTOS schedules.

A `Thread` can be in the following states:

- `Running`: The currently running thread. Only one thread at a time can be in this state.
- `Ready`: Threads that are ready to run. Once the `running` thread has terminated or is `waiting`, the `ready` thread with the highest priority becomes the `running` thread.
- `Waiting`: Threads that are waiting for an event to occur.
- `Inactive`: Threads that are not created or terminated. These threads typically consume no system resources.

<span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/thread_status.png)</span>

##### Signals

Each `Thread` can wait for signals and be notified of events:

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/rtos_signals/)](https://os.mbed.com/teams/mbed_example/code/rtos_signals/file/476186ff82cf/main.cpp)


##### Status and error codes

The CMSIS-RTOS functions will return the following statuses:

- `osOK`: function completed; no event occurred.
- `osEventSignal`: function completed; signal event occurred.
- `osEventMessage`: function completed; message event occurred.
- `osEventMail`: function completed; mail event occurred.
- `osEventTimeout`: function completed; timeout occurred.
- `osErrorParameter`: a mandatory parameter was missing or specified an incorrect object.
- `osErrorResource`: a specified resource was not available.
- `osErrorTimeoutResource`:  a specified resource was not available within the timeout period.
- `osErrorISR`: the function cannot be called from interrupt service routines (ISR).
- `osErrorISRRecursive`: function called multiple times from ISR with same object.
- `osErrorPriority`: system cannot determine priority or thread has illegal priority.
- `osErrorNoMemory`: system is out of memory; it was impossible to allocate or reserve memory for the operation.
- `osErrorValue`: value of a parameter is out of range.
- `osErrorOS`: unspecified RTOS error - runtime error but no other error message fits.
