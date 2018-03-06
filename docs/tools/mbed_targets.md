<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## Adding and configuring targets

Arm Mbed uses JSON as a description language for its build targets. You can find the JSON description of Mbed targets in `targets/targets.json` and in `custom_targets.json` in the root of a project directory. When you add new targets with `custom_targets.json`, they are added to the list of available targets. 

<span class="notes">**Note:** The Online Compiler does not support this functionality. You need to use <a href="/docs/v5.6/tools/arm-mbed-cli.html" target="_blank">Mbed CLI</a> to take your code offline.</span>

You are not allowed to redefine existing targets in `custom_targets.json`. To better understand how a target is defined, we'll use this example (taken from `targets.json`):

```
    "TEENSY3_1": {
        "inherits": ["Target"],
        "core": "Cortex-M4",
        "extra_labels": ["Freescale", "K20XX", "K20DX256"],
        "OUTPUT_EXT": "hex",
        "is_disk_virtual": true,
        "supported_toolchains": ["GCC_ARM", "ARM"],
        "post_binary_hook": {
            "function": "TEENSY3_1Code.binary_hook",
            "toolchains": ["ARM_STD", "ARM_MICRO", "GCC_ARM"]
        },
        "device_name": "MK20DX256xxx7",
        "detect_code": ["0230"]
```

The style that we use for targets is:
 - Each property is on its own line.
 - The open brace `{` is on the same line as the target name, or the property name that is its key.
 - The close brace `}` or close brace and comma `},` are on their own line.
 - Lists take up at most one line.
 - Long lines are not split.

### Standard properties

This section lists all the properties the Mbed build system understands. Unless specified otherwise, all properties are optional.

#### `inherits`

The description of an Mbed target can "inherit" from one or more descriptions of other targets. When a target, called a _child_ inherits from another target, called its _parent_, the child automatically copies all the properties from the parent. After the child has copied the properties of the parent, it may then overwrite, add or remove from those properties. In our example above, `TEENSY3_1` inherits from `Target`. This is the definition of `Target`:

```
"Target": {
    "core": null,
    "default_toolchain": "ARM",
    "supported_toolchains": null,
    "extra_labels": [],
    "is_disk_virtual": false,
    "macros": [],
    "detect_code": [],
    "public": false
}
```

Because `TEENSY3_1` inherits from `Target`:

- `core` is a property defined both in `TEENSY3_1` and `Target`. Because `TEENSY3_1` overwrites it, the value of `core` for `TEENSY3_1` is `Cortex-M4`.
- `default_toolchain` is not defined in `TEENSY3_1`. `TEENSY3_1` copies the definition of `default_toolchain` from `Target`, so the value of `default_toolchain` for `TEENSY3_1` is `ARM`.

A child target may add properties that don't exist in any of its parents. For example, `OUTPUT_EXT` is defined in `TEENSY3_1` but is not defined in `Target`.

It's possible, but discouraged, to inherit from more than one target. For example:

```
"ImaginaryTarget": {
    "inherits": ["Target", "TEENSY3_1"]
}
```

In this case, `ImaginaryTarget` inherits the properties of both `Target` and `TEENSY3_1`, so:

- The value of `ImaginaryTarget.default_toolchain` is `ARM` from `Target`.
- The value of `ImaginaryTarget.OUTPUT_EXT` is `hex` from `TEENSY3_1`.
- The value of `ImaginaryTarget.core` is `null` from `Target` because that's the first parent of `ImaginaryTarget` that defines `core`.

Avoid using multiple inheritance for your targets. If you use multiple inheritance, keep in mind that target description is similar to the Python class inheritance mechanism prior to version 2.3. The target description inheritance checks for descriptions in this order:

1. Look for the property in the current target.
1. If not found, look for the property in the first target's parent, then in the parent of the parent and so on.
1. If not found, look for the property in the rest of the target's parents, relative to the current inheritance level.

For more details about the Python method resolution order, please see <a href="http://makina-corpus.com/blog/metier/2014/python-tutorial-understanding-python-mro-class-search-path" target="_blank">this Python tutorial</a>.

#### `core`

The name of the target's Arm core.

Possible values: `"Cortex-M0"`, `"Cortex-M0+"`, `"Cortex-M1"`, `"Cortex-M3"`, `"Cortex-M4"`, `"Cortex-M4F"`, `"Cortex-M7"`, `"Cortex-M7F"`, `"Cortex-A9"`, `"Cortex-M23"`, `"Cortex-M23-NS"`, `"Cortex-M33"`, `"Cortex-M33-NS"`

<span class="notes">**Note:** Mbed OS supports v8-M architecture (Cortex-M23 and Cortex-M33) devices only with the `GCC_ARM` toolchain.</span>

#### `public`

The `public` property controls which targets the Mbed build system allows users to build. You may define targets as parents for other targets. When you define such a target, its description must set the `public` property to `false`. `Target`, shown above, sets `public` to `false` for this reason.

If `public` is not defined for a target, it defaults to `true`.

<span class="notes">**Note:** Unlike other target properties, **the value of `public` is not inherited from a parent to its children**.</span>

#### `macros`, `macros_add` and `macros_remove`

The `macros` property defines a list of macros that are available when compiling code. You may define these macros with or without a value. For example, the declaration `"macros": ["NO_VALUE", "VALUE=10"]` will add `-DNO_VALUE -DVALUE=10` to the compiler's command-line.

When a target inherits, it's possible to alter the values of `macros` in the child targets without redefining `macros` completely using `macros_add` and `macros_remove`. A child target may use `macros_add` to add its own macros and `macros_remove` to remove macros defined by its parents.

For example:

```
    "TargetA": {
        "macros": ["PARENT_MACRO1", "PARENT_MACRO2"]
    },
    "TargetB": {
        "inherits": ["TargetA"],
        "macros_add": ["CHILD_MACRO1"],
        "macros_remove": ["PARENT_MACRO2"]
    }
```

In this configuration, the value of `TargetB.macros` is `["PARENT_MACRO1", "CHILD_MACRO1"]`.

#### `extra_labels`, `extra_labels_add` and `extra_labels_remove`

The list of _labels_ defines how the build system looks for sources, include directories and so on. `extra_labels` makes the build system aware of additional directories that it must scan for such files.

When you use target inheritance, you may alter the values of `extra_labels` using `extra_labels_add` and `extra_labels_remove`. This is similar to the `macros_add` and `macros_remove` mechanism described in the previous section.

#### `features`, `features_add` and `features_remove`

The list of _features_ enables software features on a platform. Like `extra_labels`, `features` makes the build system aware of additional directories it must scan for resources. Unlike `extra_labels`, the build system recognizes a fixed set of values in the `features` list. The build system recognizes the following features:
 - `UVISOR`.
 - `BLE`.
 - `CLIENT`.
 - `IPV4`.
 - `LWIP`.
 - `COMMON_PAL`.
 - `STORAGE`.
 - `NANOSTACK`.

The following features, also recognized by the build system, are all Nanostack configurations:
 - `LOWPAN_BORDER_ROUTER`.
 - `LOWPAN_HOST`.
 - `LOWPAN_ROUTER`.
 - `NANOSTACK_FULL`.
 - `THREAD_BORDER_ROUTER`.
 - `THREAD_END_DEVICE`.
 - `THREAD_ROUTER`.
 - `ETHERNET_HOST`.
 
The build system errors when you use features outside of this list.

When you use target inheritance, you may alter the values of `features` using `features_add` and `features_remove`. This is similar to the `macros_add` and `macros_remove` mechanism the previous section describes.

#### `device_has`

The list in `device_has` defines what hardware a device has.

Mbed, libraries and application source code can then select different implementations of drivers based on hardware availability; selectively compile drivers for existing hardware only; or run only the tests that apply to a particular platform. The values in `device_has` are available in C, C++ and assembly language as `DEVICE_` prefixed macros.

#### `supported_toolchains`

The `supported_toolchains` property is the list of toolchains that support a target. The allowed values for `supported_toolchains` are `ARM`, `uARM`, `ARMC6`, `GCC_ARM` and `IAR`.

#### `default_toolchain`

The `default_toolchain` property names the toolchain that compiles code for this target in the Online Compiler. Possible values for `default_toolchain` are `ARM` or `uARM`.

#### `post_binary_hook`

Some targets require specific actions to generate a programmable binary image. Specify these actions using the `post_binary_hook` property and custom Python code. The value of `post_binary_hook` must be a JSON object with keys `function` and optionally `toolchain`. Within the `post_binary_hook` JSON object, the `function` key must contain a Python function that is accessible from the namespace of `tools/targets/__init__.py`, and the optional `toolchain` key must contain a list of toolchains that require processing from the `post_binary_hook`. When you do not specify the `toolchains` key for a `post_binary_hook`, you can assume the `post_binary_hook` applies to all toolcahins. For the `TEENSY3_1` target above, the definition of `post_binary_hook` looks like this:

```
"post_binary_hook": {
    "function": "TEENSY3_1Code.binary_hook",
    "toolchains": ["ARM_STD", "ARM_MICRO", "GCC_ARM"]
}
```

After the generation of an initial binary image for the `TEENSY3_1`, the build system calls the function `binary_hook` in the `TEENSY3_1Code` class within `tools/targets/__init__.py`.

The build tools call `TEENSY3_1` `post_binary_hook` when they build using the `ARM_STD`, `ARM_MICRO` or `GCC_ARM` toolchain.

As for the `TEENSY3_1` code, this is how it looks in `tools/targets/__init__.py`:

```
class TEENSY3_1Code:
    @staticmethod
    def binary_hook(t_self, resources, elf, binf):
        from intelhex import IntelHex
        binh = IntelHex()
        binh.loadbin(binf, offset = 0)

        with open(binf.replace(".bin", ".hex"), "w") as f:
            binh.tofile(f, format='hex')
```

The `post_build_hook` for the `TEENSY3_1` converts the output file (`binf`) from binary format to Intel HEX format. See the other hooks in `tools/targets/__init__.py` for more examples of hook code.

#### `device_name`

Use this property to pass necessary data for exporting to various third party tools and IDEs and for building applications with bootloaders.

We use the tool <a href="https://github.com/ARMmbed/mbed-os/tree/master/tools/arm_pack_manager" target="_blank">ArmPackManager</a> to parse CMSIS Packs for target information. <a href="https://github.com/ARMmbed/mbed-os/blob/master/tools/arm_pack_manager/index.json" target="_blank">`index.json`</a> stores the parsed information from the <a href="http://www.keil.com/pack/doc/CMSIS/Pack/html/" target="_blank">PDSC (Pack Description</a> retrieved from each CMSIS Pack.

The <a href="/docs/v5.6/reference/contributing-target.html">`"device_name"`</a> attribute it `targets.json` maps from a target in Mbed OS to a device in a CMSIS Pack. To support IAR and uVision exports for your target, you must add a `"device_name"` field in `targets.json` containing this key.

<a href="http://www.keil.com/pack/Keil.Kinetis_K20_DFP.pdsc" target="_blank">http://www.keil.com/pack/Keil.Kinetis_K20_DFP.pdsc</a> is the PDSC that contains TEENSY_31 device (MK20DX256xxx7). ArmPackManager has parsed this PDSC, and `index.json` stores the device information. The device information begins on line 156 of the `.pdsc` file:

```xml
<device Dname="MK20DX256xxx7">
  <processor Dfpu="0" Dmpu="0" Dendian="Little-endian" Dclock="72000000"/>
  <compile header="Device\Include\MK20D7.h"  define="MK20DX256xxx7"/>
  <debug      svd="SVD\MK20D7.svd"/>
  <memory     id="IROM1"                      start="0x00000000"  size="0x40000"    startup="1"   default="1"/>
  <memory     id="IROM2"                      start="0x10000000"  size="0x8000"     startup="0"   default="0"/>
  <memory     id="IRAM1"                      start="0x20000000"  size="0x8000"     init   ="0"   default="1"/>
  <memory     id="IRAM2"                      start="0x1FFF8000"  size="0x8000"     init   ="0"   default="0"/>
  <algorithm  name="Flash\MK_P256.FLM"        start="0x00000000"  size="0x40000"                  default="1"/>
  <algorithm  name="Flash\MK_D32_72MHZ.FLM"   start="0x10000000"  size="0x8000"                   default="1"/>
  <book name="Documents\K20P100M72SF1RM.pdf"         title="MK20DX256xxx7 Reference Manual"/>
  <book name="Documents\K20P100M72SF1.pdf"           title="MK20DX256xxx7 Data Sheet"/>
</device>
```

The `device_name` key in `targets.json` is `MK20DX256xxx7` for any target that uses this particular MCU.

#### `OUTPUT_EXT`

The `OUTPUT_EXT` property controls the file type emitted for a target by the build system. You may set `OUTPUT_EXT` to `bin` for binary format, `hex` for Intel HEX format and `elf` for ELF format. We discourage using the `elf` value for `OUTPUT_EXT` because the build system must always emit an ELF file.

#### `default_lib`

The `delault_lib` property controls which library, small or standard, the `GCC_ARM` toolchain links. The `default_lib` property may take on the values `std` for the standard library and `small` for the reduced size library.

#### `bootloader_supported`

The `bootloader_supported` property controls whether the build system allows a bootloader or a bootloader-using application to be built for a target. The default value of `bootloader_supported` is `false`.

#### `release_versions`

The `release_versions` property is a list of major versions of Mbed OS that the target supports. The list within `release_versions` may only contain `2`, indicating that the support of Mbed OS 2, and `5`, indicating the support of Mbed OS 5. We build all targets that are released for Mbed OS 2 as a static library. Targets are released for Mbed OS 2 by putting a `2` in the `release_version` list.

#### `supported_form_factors`

The `supported_form_factors` property is an optional list of form factors that a development board supports. You can use this property in C, C++ and assembly language by passing a macro prefixed with `TARGET_FF_` to the compiler. The accepted values for `supported_form_factors` are `ARDUINO`, which indicates compatibility with Arduino headers, and `MORPHO`, which indicates compatibility with ST Morpho headers.

### Style guide

A linting script for `targets.json` is available as `tools/targets/lint.py` in Mbed OS. This script is a utility for avoiding common errors when defining targets and detecting style inconsistencies between targets. This linting script displays style errors based on a few rules outlined below.

#### Rules enforced

There are two sets of rules: rules that affect how you must structure target inheritance and rules that govern what each role within the inheritance hierarchy can do.

##### Inheritance rules

A target's inheritance must look like one of these:

```
MCU -> Board
MCU -> Module -> Board
Family -> MCU -> Board
Family -> MCU -> Module -> Board
Family -> Subfamily -> MCU -> Board
Family -> Subfamily -> MCU -> Module -> Board
```

The linting script guesses where the Boards and Modules stop and the MCUs, Families and Subfamilies begin. An MCU, Family or Subfamily must have at least one Board or Module above it in any hierarchy.

##### Role rules

For each of these target roles, some restrictions are in place:
- Families, MCUs and Subfamilies may contain the following keys:
  - `core`.
  - `extra_labels`.
  - `features`.
  - `bootloader_supported`.
  - `device_name`.
  - `post_binary_hook`.
  - `default_tool chain`.
  - `config`.
  - `target_overrides`.
- MCUs are required to have, and Families and Subfamilies may have:
  - `release_versions`.
  - `supported_toolchains`.
  - `default_lib`.
  - `public`.
  - `device_has`.
- Modules and Boards may have the following keys:
  - `supported_form_factors`.
  - `is_disk_virtual`.
  - `detect_code`.
  - `extra_labels`.
  - `public`.
  - `config`.
  - `forced_reset_timeout`.
  - `target_overrides`
- `macros` are not used. That is intentional: they do not provide any benefit over `config` and `target_overrides` but can be very difficult to use. In practice it is very difficult to override the value of a macro with a value. `config` and `target_overries`, on the other hand, are designed for this use case.
- `extra_labels` may not contain any target names
- `device_has` may only contain values from the following list:
  - `ANALOGIN`.
  - `ANALOGOUT`.
  - `CAN`.
  - `ETHERNET`.
  - `EMAC`.
  - `FLASH`.
  - `I2C`.
  - `I2CSLAVE`.
  - `I2C_ASYNCH`.
  - `INTERRUPTIN`.
  - `LOWPOWERTIMER`.
  - `PORTIN`.
  - `PORTINOUT`.
  - `PORTOUT`.
  - `PWMOUT`.
  - `RTC`.
  - `TRNG`.
  - `SERIAL`.
  - `SERIAL_ASYNCH`.
  - `SERIAL_FC`.
  - `SLEEP`.
  - `SPI`.
  - `SPI_ASYNCH`.
  - `SPISLAVE`.
- If `release_versions` contains 5, then `supported_toolchains` must contain all of `GCC_ARM`, `ARM` and `IAR`
- MCUs, Families and SubFamilies must set `public` to `false`

#### Sample output

The linting script takes three subcommands: `targets`, `all-targets` and `orphans`.

##### `targets` and `all-targets` commands 

The `targets` and `all-targets` commands both show errors within public inheritance hierarchies. For example:

`python tools/targets/lint.py targets EFM32GG_STK3700 EFM32WG_STK3800 LPC11U24_301`

Could produce this output

```yaml
hierarchy: Family (EFM32) -> MCU (EFM32GG990F1024) -> Board (EFM32GG_STK3700)
target errors:
  EFM32:
  - EFM32 is not allowed in extra_labels
  EFM32GG990F1024:
  - macros found, and is not allowed
  - default_lib not found, and is required
  - device_has not found, and is required
  EFM32GG_STK3700:
  - progen found, and is not allowed
  - device_has found, and is not allowed
---
hierarchy: Family (EFM32) -> MCU (EFM32WG990F256) -> Board (EFM32WG_STK3800)
target errors:
  EFM32:
  - EFM32 is not allowed in extra_labels
  EFM32WG990F256:
  - macros found, and is not allowed
  - default_lib not found, and is required
  - device_has not found, and is required
  EFM32WG_STK3800:
  - progen found, and is not allowed
  - device_has found, and is not allowed
---
hierarchy: Family (LPCTarget) -> MCU (LPC11U24_301) -> ???
hierarchy errors:
- no boards found in heirarchy
target errors:
  LPC11U24_301:
  - release_versions not found, and is required
  - default_lib not found, and is required
  - public not found, and is required
```

The `all-targets` command is very verbose, with output that matches the format above but is too long to reproduce here.

##### `orphans` command

The `orphans` command shows all targets that you cannot reach from a public target.

`python tools/targets/lint.py orphans`

```yaml
- CM4_UARM
- CM4_ARM
- CM4F_UARM
- CM4F_ARM
- LPC1800
- EFR32MG1P132F256GM48
- EFR32MG1_BRD4150
```
