<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
# mbed OS Software Design Guide

<span class="tips">
Principles of mbed software:
</br>
- Consistent </br> 
- Intuitive </br>
- Simple </br>
- Reliable </br>
</span>

## Style

Please refer to the [mbed style guide](code_style.md).

## Organization

The mbed OS codebase is organized into conceptual submodules to limit the scope and complexity of individual contributions. These modules are contained in the mbed OS codebase as a single Git repo. We suggest this model for external libraries.
 
- Modules should be logically grouped in the OS tree. Avoid generic words; be intentional with naming.

    ```
    - mbed-os
        |- rtos - user API + any layer required to fit into mbed OS
        |   |- rtx - third party implementation
        |   |- windows - third party implementation
        |   `- tests
        |       |- unit - rtos related unit tests
        |       `- functional - rtos related functional tests
        |- drivers - user API on-chip hardware interfaces (Ticker, DigitalOut, etc)
        |- util - chip-independent layer (retarget, toolchain, etc)
        |   `- bootloader - chip-independent bootloader
        |- net - networking API
        |   |- stack - network implementations  
        |   |   |-lwip
        |   |   `- nanostack
        |   `- tests - network tests
        `- tests
        |- integration - mbed OS integration tests
        `- smoke - mbed OS smoke tests (blinky, etc)
    ```

- Prefix each source file with the module name followed by an underscore. This prevents conflicts with other similarly named files in different modules such as `nanostack/thread.c` and `drivers/Thread.cpp`; not all toolchains are able to support object files with the same name.

    ```
    mbed-os/rtos/rtos_thread.cpp
    mbed-os/rtos/rtos_semaphore.cpp
    mbed-os/drivers/drivers_analog_in.cpp
    ```

- Always include header files using the module directory in the path. For example: `#include “lwip/lwip-interface.h”`, `#include “drivers/Ticker.h”`.
	Limit the include path to the module directory. This allows moving the module in the future.
- As an entry point for the module (from the user space), we suggest a single header file. For example: `mbed.h`, `rtos.h`.
- Header files should limit external includes to avoid indirectly exposing unrelated APIs. Header files should not expand namespaces.
- In C++ modules, the API should be contained in a namespace that matches the module’s name. For example: `mbed::Ticker`, `rtos::Thread`, `netsocket::Socket`.
- In C modules, every nonstatic function and type should be prefixed with the module’s name followed by an underscore. For example: `mbed_critical_section_enter()`, `lwip_gethostbyname(host)`.
- A module contained in the mbed OS codebase may be mirrored in a separate repo. The source repo should be clearly identified and linked to from the module's README.
- Special directories should follow consistent naming convention.
 
## Contribution
1. Please refer to the [mbed contribution guide](contributing.md).
1. Each pull request should serve a single purpose.
1. The code must compile every commit.
1. Commit message should be prefixed with the submodule name and a colon:
	
    ```
    lwip: Fixed buffer overrun in rx loop 
    The rx loop did not properly wait for rx semaphore to release
    causing the buffer to overrun
    ```

1. Patches must land on master before being backported to one or more release branches.
1. Feature development may happen in a separate branch, and brought to master when complete.
1. The master branch and release branches must never be rewritten.
1. All contributors must sign the CLA: [https://developer.mbed.org/contributor_agreement](https://developer.mbed.org/contributor_agreement).
1. For incoming sources, the only acceptable licenses are:
    - MIT.
    - Apache.
    - Permissive Binary License.

## API design

A general module can be split into two APIs, the frontend (or user API) and the backend (or porting layer). The user API describes the programmer interface that the library implements. For mbed OS, the user-facing API should adopt a C++ class-based interface, while the porting layer should adopt a C-compatible interface.

### API design - user API

- Each module should provide an object-oriented C++ user API.
- The current standard is strictly C++03 (for portability).
- State should be contained in relevant C++ classes.
- Think twice before adopting language features that don't exist in the codebase:
	- Think C with the good parts of C++.
	- Don't make users learn new things.
	- Exceptions and RTTI are disabled.
	- Avoid the STL due to unknown effect on system resources.
- Prefer C language features over C++ language features:
	- Use different function names over ambiguous overloads.
	- Do use `uint8_t read_8(void)`, `uint16_t read_16(void)`, `uint32_t read_32(void)`.
	- Do use `void write_8(uint8_t)`,  `void write_16(uint16_t)`, `void write_32(uint32_t)`.
	- Limit templates to types and array-sizes.
- Use pointers and references based on the following rules. Use appropriate documentation where ownership may be ambiguous:
	- Use a copy or immutable reference to pass types with value semantics.
	- Use a pointer to borrow ownership of dynamic polymorphic classes.
	- Use a pointer to transfer ownership of dynamic polymorphic classes. Clearly document transferred ownership.
	- Use pointers over references:
 		- More familiar to C users.
		- Ownership is syntactically clear.
- Organize classes into two types:
	- Types with value semantics such as SocketAddress and CallbackL.
		- User-friendly alternatives to builtin-types.
		- Cannot be extended (suffer from object slicing).
		- Pass by value, no memory management needed (handled on stack).
		- If possible, pass by const-reference to reduce copying.
 		- Must be cheap to copy.
		- Should be small (<= 16 bytes). This does not include indirectly referenced memory.
	- Dynamic polymorphic classes such as Ticker and EthernetInterface:
		- Participate in class hierarchy.
		- Can be abstracted by interfaces.
		- Extendable - should have virtual table. Must contain virtual destructor if overloadable.
		- Passed by pointer; memory management should be left up to user.
		- Should declare copy constructor and copy assignment operator as private. If copying is really needed, we suggest a virtual `clone` member function, to avoid issues with slicing and indicate that the clone is a nontrivial operation.
		- If a class contains a very large memory region (> 64 bytes), prefer dynamically allocating the region; it prevents stack overflows if the user instantiates the class on the stack.
- A class should have one responsibility. For example: `UDPSocket` vs `TCPSocket`, `Ticker` vs `Timer`.
- Use inheritance for an “is a” relationship. For example: `UDPSocket` and `Socket`.
	- Prefer abstract base classes over template-based polymorphism to avoid code size increase.
	- Prefer composing abstract interfaces in C++ code over preprocessor-based conditional compilation and other forms of indirect dispatch.
1. Do not declare objects in global scope (applications should allocate global objects). Objects declared in global scope rarely get garbage-collected by compilers during link time. This can cause significant bloat in the size of an application.
1. Use get/set functions with private member variables to hide internal state from the user.
1. Avoid operations that can fail if you can't signal an error. Class constructors should not fail.
1. Nonrecoverable errors (such as OOM and mutex lock in interrupt) should not return to users.
1. Recoverable errors (such as UDP packet loss) should be propagated to the user via error code.

### API design - porting layer

- Each module should provide a C-compatible porting layer.
- The current standards are strictly C99 (for portability).
- The porting layer should make no assumptions about how it is consumed.
- State should be contained in a struct passed by pointer from the user API. Avoid global state.
- The porting layer should be designed to allow as much variance in the implementation as is reasonable.
- Simplicity is beautiful.

### Thread and IRQ safety

[Full documentation](../concepts/thread_safety.md).

- User APIs should be thread safe.
- If a user API is intended to be interrupt safe, this should be clearly documented.
- If a user API is unable to be thread safe, this should be clearly documented with warning notation. 
	Use a consistent form across all APIs: **"warning: not thread safe"**.
- A module’s porting layer should be designed for implementations that are not thread safe.
- If a callback is called in interrupt context, the API responsible should be clearly documented with a warning. 
	Use a consistent form across all APIs: **"warning: called from interrupt context"**

## Documentation

- Each function and class in a module should provide a doxygen comment that documents the function and each argument and return value:

    ``` cpp
    /** Wait until a Mutex becomes available.
     *
     * @param   millisec  timeout value or 0 in case of no time-out. (default: osWaitForever)
     * @return  status code that indicates the execution status of the function.
     */
     osStatus lock(uint32_t millisec=osWaitForever);
    ```

- The doxygen of each class's header file should contain a simple use example. 
- Each module should provide a README that documents the module:
	- The README should start with a small paragraph describing the module to users with no prior knowledge.
	- The README should contain a code example showing how to use the module.
	- If a module contains a porting layer, the README should include porting instructions.
	- If a module contains tests, the README should provide testing instruction.
- Extended documentation should be located in the module’s `docs` directory with appropriate links from the module’s README.

## Testing

[Full documentation](../advanced/testing.md).

- Each module should contain a `tests` directory with tests that cover the module’s functionality.
- Tests should be organized based on the class being tested; roughly one test file per class.
- Tests included in the codebase must be compatible with the mbed OS test framework.
- To avoid regressions, every bug fix should include an additional test case that identifies the bug and deterministically fails before the bug is fixed.
 
## Configuration

mbed OS provides a powerful configuration system for application development. However, modules should also be concerned with remaining configurable outside of the mbed build system. Modules should provide well-documented configuration options in a simple header file.

[Full documentation](../advanced/config_system.md).
 
- Each module should provide a `module_lib.json` (or similar) with configuration options.
- Each config option should contain documentation covering its purpose and effect on the system.
- To help port new targets, each config option should provide a reasonable default (in case the config option is not defined).
- Config options should not change the behavior of the API. 
	- Prefer multiple classes where different functionality is needed in the user API.
- Targets and applications should be able to override each configuration.
- The default choice of optimization should be size, on all platforms.
