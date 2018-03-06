<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Keil uVision

This document explains how to build and debug Arm Mbed OS applications using Keil uVision 5. Due to the linker limits, this does not work in the free version of uVision. If you do not have a uVision license, you can use <a href="/docs/v5.6/tutorials/eclipse.html" target="_blank">Eclipse</a>, <a href="/docs/v5.6/tutorials/visual-studio-code.html" target="_blank">Visual Studio Code</a> or any other IDE that supports debugging through GDB. For more info, please see <a href="https://os.mbed.com/docs/v5.6/tools/setting-up-a-local-debug-toolchain.html" target="_blank">Setting up a local debug toolchain</a>.

### Exporting a project

To export your project to uVision, you can use either the Online Compiler or Mbed CLI.

<span class="notes">**Note:** Store the project on your local hard drive. uVision does not support building from a network share.</span>

#### Online Compiler

1. Right click on your project.
1. Select *Export Program...*.
1. Under 'Export toolchain', select *Keil uVision 5*.
1. Click *Export*, and unpack at a convenient location.

<span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/uvision1.png)<span>Exporting using the Arm Mbed Online Compiler</span></span>

#### Arm Mbed CLI

1. In your project folder, run:

    ```
    # replace K64F with your target board
    $ mbed export -i uvision5 -m K64F --profile mbed-os/tools/profiles/debug.json
    ```

### Starting a debug session

The exported project contains a `.uvprojx` file. Double click on this file to open the project in uVision. uVision 5 does not support nested folders in the tree, so find your application source code by looking for a folder with the same name as your project.

<span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/uvision2.png)<span>Debugging an Mbed OS 5 program in uVision 5</span></span>

To build your project and start a debug session:

1. Click *Project > Build Target*.
1. When building succeeds, click *Debug > Start/Stop Debug Session*.
1. If uVision does not connect to your development board, go to *Project > Options for Target > Debug*, and make sure 'CMSIS-DAP Debugger' is selected.

<span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/uvision3.png)<span>CMSIS-DAP Debugger options</span></span>

For more information on the CMSIS-DAP Debugger driver in uVision, see the <a href="http://www.keil.com/support/man/docs/dapdebug/dapdebug_drv_cfg.htm" target="_blank">uVision documentation</a>.
