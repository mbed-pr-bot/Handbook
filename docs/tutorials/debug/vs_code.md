<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Visual Studio Code

This document explains how to build and debug Arm Mbed OS applications using Visual Studio Code. Before starting, first <a href="/docs/v5.6/tools/setting-up-a-local-debug-toolchain.html" target="_blank">configure your local debug toolchain</a>.

### Installing Visual Studio Code

You need to install Visual Studio Code with the C/C++ extensions to begin.

1. Install <a href="https://code.visualstudio.com" target="_blank">Visual Studio Code</a>.
1. Open Visual Studio Code, and click on the **Extensions** button.
1. Search for the C/C++ plugin (by Microsoft) and click **Install**.

    <span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/vscode2.png)<span>Installing the C/C++ plugin in Visual Studio Code</span></span>

1. When prompted, restart the IDE.

### Exporting a project

To export your project to Visual Studio Code, you can use either the Online Compiler or Arm Mbed CLI.

#### Online Compiler

1. Right click on your project.
1. Select **Export Program...**.
1. Under **Export toolchain**, select **Visual Studio Code (GCC ARM)**.

    <span class="tips">**Tip:** For most targets, you can also export to IAR or ARMCC.</span>

1. Click **Export**, and unpack at a convenient location.

    <span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/vscode1.png)<span>Exporting to Visual Studio Code</span></span>

#### Arm Mbed CLI

In your project folder, run:

```
# alternatively, use -i vscode_armc5 for ARMCC, or -i vscode_iar for IAR
# replace K64F with your target board

$ mbed export -i vscode_gcc_arm -m K64F --profile mbed-os/tools/profiles/debug.json
```

### Configuring the debugger

To configure the debugger for your project:

1. Open the folder in Visual Studio Code.
1. Open the `.vscode/launch.json` file.
1. If you're using pyOCD as your debug server, verify that `debugServerPath` is set to the location of `pyocd-gdbserver`.
1. If you're using OpenOCD as your debug server:
     1. Change `debugServerPath` to point to the location of `openocd`.
     1. Change `debugServerArgs` to include your OpenOCD arguments. For more info, read our <a href="https://os.mbed.com/docs/v5.6/tools/exporting.html" target="_blank">toolchain document</a>.

    <span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/vscode3.png)<span>Configuring the debugger</span></span>

<span class="notes">**Note:** If you installed the GNU Arm Embedded Toolchain in a nondefault location (for example, through the Arm Mbed CLI installer), you need to update the `MIDebuggerPath` to the full path of your copy of `arm-none-eabi-gdb`. To find the new path, open a terminal, and run `where arm-none-eabi-gdb` (Windows) or `which arm-none-eabi-gdb` (macOS and Linux).</span>

### Debugging your project

1. On the **Debug** tab, click the **Play** icon.

    <span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/vscode4.png)<span>Starting the debug session</span></span>

1. The project builds, and debugging starts when the build succeeds.
1. To see warnings or errors, select **View > Problems**.
1. Click on the **Debug Console** button to see the debug output (this is not activated automatically).

    <span class="images">![](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/vscode5.png)<span>Running the debugger</span></span>

<span class="tips">**Tip:** You can use the Debug Console to interact with the device over GDB and use functionality the UI does not expose. For example, to see the registers, type `-exec info registers`. To put a watch on a memory location, type `-exec watch *0xdeadbeef`.</span>
