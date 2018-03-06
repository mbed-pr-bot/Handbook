<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Bootstrap

### Entry points

Mbed OS provides two entry points for developers to hook into:

- `main(void)` - Default entry point. All the standard application code goes here.
- `mbed_main(void)` - Executed directly before `main`. The user can define this.

When execution reaches the entry points, a user can expect a fully initialized system that is ready to execute application code. For this to happen, the following must have occurred prior to this point:

- Low-level platform initialization.
- Stack and heap initialization.
- Vector table copied to RAM.
- Standard library initialized.
- RTOS initialized and scheduler started.

### Retargeting

Mbed OS redefines multiple standard C library functions to enable them to work in a predictable and familiar way on a remote embedded target device:

- `stdin`, `stdout`, `stderr` - These file descriptors are pointing to the serial interface to enable users to use standard input/output functions, such as `printf` or `getc`.
- `fopen`, `fclose`, `fwrite`, `fread`, `fseek` and other standard file operations - Enable the user to work with the serial interface, as well as the built-in file system.
- `opendir`, `readdir`, `closedir` and other standard directory operations - Enable users to use built in file system.
- `exit` - It causes the board to stop current execution, flush the standard file handles, close the semihosting connection and enter an infinite loop. If the return code indicates an error, the board blinks error patterns on the built-in LED.
- `clock` - Overloaded to use platform's microsecond ticker.
