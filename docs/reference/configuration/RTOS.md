<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
<h2 id="configuration-rtos">RTOS</h2>

**Thread memory model**

All threads in Mbed OS share a global heap. By default, mbed OS dynamically allocates memory for thread stacks from the global heap. User spawned thread stacks can be allocated from other memory areas. The size of the global heap depends on the compiler. The heap size is static when compiling with IAR, and it is different for each target in Mbed OS. You can find it in the IAR linker configuration files nested inside target directories. For example, you can find the <a href="https://github.com/ARMmbed/mbed-os/blob/master/targets/TARGET_Freescale/TARGET_MCUXpresso_MCUS/TARGET_K66F/device/TOOLCHAIN_IAR/MK66FN2M0xxx18.icf#L49-L51" target="_blank">linker configuration file</a> for a K66F. For the GCC and ARM compilers, the heap size is dynamic based on RAM usage.

**Default threads**

By default, there are four threads running after boot: the ISR/scheduler thread, the idle thread, the timer thread and the main application thread. Combined, the idle thread, timer thread and ISR/scheduler thread consume 2 kilobytes of RAM. The user cannot modify these. You can expand or reduce the size of the main application thread stack by using the Mbed configuration system.

```
Configuration parameters
------------------------

Name: rtos.present
    Defined by: library:rtos
    Macro name: MBED_CONF_RTOS_PRESENT
    Value: 1 (set by library:rtos)

Name: events.present
    Defined by: library:events
    Macro name: MBED_CONF_EVENTS_PRESENT
    Value: 1 (set by library:events)
Name: events.shared-dispatch-from-application
    Description: No thread created for shared event queue - application will call dispatch from another thread (eg dispatch_forever at end of main)
    Defined by: library:events
    No value set
Name: events.shared-eventsize
    Description: Event buffer size (bytes) for shared event queue
    Defined by: library:events
    Macro name: MBED_CONF_EVENTS_SHARED_EVENTSIZE
    Value: 256 (set by library:events)
Name: events.shared-highprio-eventsize
    Description: Event buffer size (bytes) for shared high-priority event queue
    Defined by: library:events
    Macro name: MBED_CONF_EVENTS_SHARED_HIGHPRIO_EVENTSIZE
    Value: 256 (set by library:events)
Name: events.shared-highprio-stacksize
    Description: Stack size (bytes) for shared high-priority event queue thread
    Defined by: library:events
    Macro name: MBED_CONF_EVENTS_SHARED_HIGHPRIO_STACKSIZE
    Value: 1024 (set by library:events)
Name: events.shared-stacksize
    Description: Stack size (bytes) for shared event queue thread
    Defined by: library:events
    Macro name: MBED_CONF_EVENTS_SHARED_STACKSIZE
    Value: 1024 (set by library:events)
```
