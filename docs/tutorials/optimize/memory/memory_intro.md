<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Memory Optimization

Beginning with Mbed OS 5, new features such as RTOS created an increase in flash and RAM usage. This guide explains how to optimize program memory usage for release builds using Mbed OS 5.

<span class="notes">**Note:** More information about the memory usage differences between Mbed OS 2 and Mbed OS 5 is available in <a href="https://os.mbed.com/blog/entry/Optimizing-memory-usage-in-mbed-OS-52/" target="_blank">a blog post</a>.</span>

### Removing unused modules

For a simple program like <a href="https://github.com/ARMmbed/mbed-os-example-blinky" target="_blank">Blinky</a>, a program that flashes an LED, typical memory usage is split among the following modules:

```
+---------------------+-------+-------+-------+
| Module              | .text | .data |  .bss |
+---------------------+-------+-------+-------+
| Fill                |   132 |     4 |  2377 |
| Misc                | 28807 |  2216 |    88 |
| features/frameworks |  4236 |    52 |   744 |
| hal/common          |  2745 |     4 |   325 |
| hal/targets         | 12172 |    12 |   200 |
| rtos/rtos           |   119 |     4 |     0 |
| rtos/rtx            |  5721 |    20 |  6786 |
| Subtotals           | 53932 |  2312 | 10520 |
+---------------------+-------+-------+-------+
```

Even if you are no longer testing your program, the `features/frameworks` module includes the Mbed OS test tools. Because of this, you are building one of our test harnesses into every binary. <a href="https://github.com/ARMmbed/mbed-os/pull/2559" target="_blank">Removing this module</a> saves a significant amount of RAM and flash memory.

#### `Printf` and UART

The linker can also remove other modules that your program does not use. For example, <a href="https://github.com/ARMmbed/mbed-os-example-blinky" target="_blank">Blinky's</a> `main` program doesn't use `printf` or UART drivers. However, every Mbed OS module handles traces and assertions by redirecting their error messages to `printf` on serial output - forcing the `printf` and UART drivers to be compiled in and requiring a large amount of flash memory.

To disable error logging to serial output, set the `NDEBUG` macro and the following configuration parameter in your program's `mbed_app.json` file:

```
{
    "macros": [
        "NDEBUG=1"
    ],
    "target_overrides": {
        "*": {
            "platform.stdio-flush-at-exit": false
        }
    }
}
```

<span class="notes">**Note:** Different compilers, different results; compiling with one compiler yields different memory usage savings than compiling with another.</span>

#### Embedded targets

You can also take advantage of the fact that these programs only run on embedded targets. When you run a C++ application on a desktop computer, the operating system constructs every global C++ object before calling `main`. It also registers a handle to destroy these objects when the program ends. The code the compiler injects has some implications for the application:

- The code that the compiler injects consumes memory.
- It implies dynamic memory allocation and thus requires the binary to include `malloc`, even when the application does not use it.

When you run an application on an embedded device, you don't need handlers to destroy objects when the program exits, because the application will never end. You can save more RAM and flash memory usage by <a href="https://github.com/ARMmbed/mbed-os/pull/2745" target="_blank">removing destructor registration</a> on application startup and by eliminating the code to destruct objects when the operating system calls `exit()` at runtime.
