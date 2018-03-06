<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
# Blinky on mbed CLI

## Quick start video

<span class="tips">**Tip:** the video assumes you've already [installed mbed CLI](#installing-mbed-cli-and-a-toolchain).

<span class="images">[![Video tutorial](http://img.youtube.com/vi/PI1Kq9RSN_Y/0.jpg)](https://www.youtube.com/watch?v=PI1Kq9RSN_Y)<span>Watch how to create your first application on mbed CLI</span></span>

## Blinky's code

```c++
#include "mbed.h"
#include "rtos.h"

DigitalOut led1(LED1);

// main() runs in its own thread in the OS
// (note the calls to wait below for delays)
int main() {
    while (true) {
        led1 = !led1;
        wait(0.5);
    }
}

```

## Installing mbed CLI and a toolchain

mbed CLI is an offline tool, meaning you'll have to install it before you can work. You will also need to install a toolchain. Please follow the installation instructions on the [mbed CLI page](../dev_tools/cli.md), and come back here when you're done.

## Setting context

Whenever you work with mbed CLI, you need to navigate your command-line terminal to the directory in which you want to work. For example, if your program is in a folder called ``my_program``:

```
cd my_program
mbed <commands>
```

## Getting Blinky

mbed CLI can import Blinky, along with the mbed OS codebase. The import process creates a new directory as a subdirectory of your current context (as explained above). 

To import Blinky, from the command-line:

1. Navigate to a directory of your choice. We're navigating to our development directory:

    ``cd dev_directory``

1. Import the example:

    ```
    mbed import mbed-os-example-blinky
    cd mbed-os-example-blinky
    ```
    **Tip:** ``import`` requires a full URL to Mercurial or GitHub. If you don't enter a full URL, mbed CLI prefixes your snippet with ``https://github.com/ARMmbed/``. We took advantage of this feature in our example; ``import mbed-os-example-blinky`` is interpreted as ``https://github.com/ARMmbed/mbed-os-example-blinky``.

Blinky is now under ``dev_directory`` > `` mbed-os-example-blinky``. You can look at ``main.cpp`` to familiarize yourself with the code.

## Compiling

Invoke `mbed compile`, specifying:

* Your board: ``-m <board_name>``.
* Your toolchain: ``-t <GCC_ARM`, `ARM` or `IAR`>``.

For example, for the board K64F and the ARM Compiler 5:

```
mbed compile -m K64F -t ARM
```

If you don't know the name of your target board, there are several ways to tell.

* Invoke `mbed detect`, and the mbed CLI tool displays an output similar to below, where 'K64F' is the name of your target platform and COM3 is the name of the serial port that the platform connects to:

```
[mbed] Detected "K64F" connected to "D:" and using com port "COM3"
```

* Check the board information page on the list of [mbed Enabled boards](https://developer.mbed.org/platforms/). The right-hand side of each information page lists the name of the target.

* If you only have one mbed Enabled board connected, mbed CLI can automatically detect the target by specifing `-m detect`.

Your PC may take a few minutes to compile your code. At the end you should get the following result:

```
[snip]
+----------------------------+-------+-------+------+
| Module                     | .text | .data | .bss |
+----------------------------+-------+-------+------+
| Misc                       | 13939 |    24 | 1372 |
| core/hal                   | 16993 |    96 |  296 |
| core/rtos                  |  7384 |    92 | 4204 |
| features/FEATURE_IPV4      |    80 |     0 |  176 |
| frameworks/greentea-client |  1830 |    60 |   44 |
| frameworks/utest           |  2392 |   512 |  292 |
| Subtotals                  | 42618 |   784 | 6384 |
+----------------------------+-------+-------+------+
Allocated Heap: unknown
Allocated Stack: unknown
Total Static RAM memory (data + bss): 7168 bytes
Total RAM memory (data + bss + heap + stack): 7168 bytes
Total Flash memory (text + data + misc): 43402 bytes
Image: .\.build\K64F\ARM\mbed-os-example-blinky.bin             
```

The program file, ``mbed-os-example-blinky.bin``, is under your ``build\K64F\ARM\`` folder.

## Programming your board

mbed Enabled boards are programmable by drag and drop over a USB connection.

1. Connect your mbed board to the computer over USB.
1. Copy the binary file to the board. In the example above, the file is ``mbed-os-example-blinky.bin``, and it's under the ``build\K64F\ARM\`` folder.
1. Press the reset button to start the program.

You should see the LED of your board turning on and off.
