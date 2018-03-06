<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
# Adding and configuring mbed targets

mbed uses JSON as a description language for its build targets. The JSON description of mbed targets can be found in `targets/targets.json`. To better understand how a target is defined, we'll use this example (taken from `targets.json`):

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

The definition of the target called **TEENSY3_1** is a JSON object. The properties in the object are either "standard" (understood by the mbed build system) or specific to the target.

# Standard properties

This section lists all the properties that are known to the mbed build system. Unless specified otherwise, all properties are optional.

## inherits

The description of an mbed target can "inherit" from one or more descriptions of other targets. When a target **A** inherits from another target **B**, (**A** is the _child_ of **B** and **B** is the _parent_ of **A**), it automatically "borrows" all the definitions of properties from **B** and can modify them as needed (if you're familiar with Python, this is very similar to class inheritance). In our example above, `TEENSY3_1` inherits from `Target` (most mbed targets inherit from `Target`). This is how `Target` is defined:

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

- `core` is a property defined both in `TEENSY3_1` and `Target`. Because `TEENSY3_1` redefines it, the value of `core` for `TEENSY3_1` is `Cortex-M4`.
- `default_toolchain` is not defined in `TEENSY3_1`, but because it is defined in `Target`, `TEENSY3_1` borrows it, so the value of `default_toolchain` for `TEENSY3_1` is `ARM`.

A target can add properties that don't exist in its parent(s). For example, `OUTPUT_EXT` is defined in `TEENSY3_1`, but doesn't exist in `Target`.

It's possible to inherit from more than one target. For example:

```
"ImaginaryTarget": {
    "inherits": ["Target", "TEENSY3_1"]
}
```

In this case, `ImaginaryTarget` inherits the properties of both `Target` and `TEENSY3_1`, so:

- The value of `ImaginaryTarget.default_toolchain` is `ARM` (from `Target`).
- The value of `ImaginaryTarget.OUTPUT_EXT` is `hex` (from `TEENSY3_1`).
- The value of `ImaginaryTarget.core` is `null` (from `Target`, because that's the first parent of `ImaginaryTarget` that defines `core`).

Avoid using multiple inheritance for your targets if possible because it can get pretty tricky to figure out how a property is inherited. If you have to use multiple inheritance, keep in mind that the mbed target description mechanism uses the old (pre 2.3) Python mechanism for finding the method resolution order:

1. Look for the property in the current target.
1. If not found, look for the property in the first target's parent, then in the parent of the parent and so on.
1. If not found, look for the property in the rest of the target's parents, relative to the current inheritance level.

For more details about the Python method resolution order, check [this link](http://makina-corpus.com/blog/metier/2014/python-tutorial-understanding-python-mro-class-search-path).

## core

The name of the target's ARM core.

Possible values: `"Cortex-M0"`, `"Cortex-M0+"`, `"Cortex-M1"`, `"Cortex-M3"`, `"Cortex-M4"`, `"Cortex-M4F"`, `"Cortex-M7"`, `"Cortex-M7F"`, `"Cortex-A9"`

## public

Some mbed targets may be defined solely for the purpose of serving as an inheritance base for other targets (as opposed to being used to build mbed code). When such a target is defined, its description must have the `public` property set to `false`, to prevent the mbed build system from considering it as a build target. An example is the `Target` target shown above.

If `public` is not defined for a target, it defaults to `true`.

<span class="notes">**Note:** Unlike other target properties, **the value of `public` is not inherited from a parent to its children**.</span>

## macros, macros_add, macros_remove

The macros in this list will be defined when compiling mbed code. The macros can be defined with or without a value. For example, the declaration `"macros": ["NO_VALUE", "VALUE=10"]` will add these definitions to the compiler's command-line: `-DNO_VALUE -DVALUE=10`.

When target inheritance is used, it's possible to alter the values of `macros` in inherited targets without redefining `macros` completely:

- An inherited target can use `macros_add` to add its own macros.
- An inherited target can use `macros_remove` to remove macros defined by its parents.

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

## extra_labels, extra_labels_add, extra_labels_remove

The list of **labels** defines how the build system looks for sources, libraries, include directories and any other files that are needed at compile time. `extra_labels` makes the build system aware of additional directories that must be scanned for such files.

If target inheritance is used, it's possible to alter the values of `extra_labels` using `extra_labels_add` and `extra_labels_remove`. This is similar to the `macros_add` and `macros_remove` mechanism described in the previous paragraph.

## features, features_add, features_remove

The list of **features** defines what hardware a device has.

mbed, libraries or application source code can then select different implementations of drivers based on hardware availability; selectively compile drivers for existing hardware only; or run only the tests that apply to a particular platform.

If target inheritance is used, it's possible to alter the values of `features` using `features_add` and `features_remove`. This is similar to the `macros_add` and `macros_remove` mechanism described in the previous two paragraphs.

## supported_toolchains

This is the list of toolchains that can be used to compile code for the target. The known toolchains are `ARM`, `uARM`, `GCC_ARM`, `GCC_CR`, `IAR`.

## default_toolchain

The name of the toolchain that will be used by default to compile this target (if another toolchain is not specified). Possible values are `ARM` or `uARM`.

## post_binary_hook

Some mbed targets require specific actions for generating a binary image that can be flashed to the target. If that's the case, these actions can be specified using the `post_binary_hook` property and custom Python code. For the `TEENSY3_1` target above, the definition of `post_binary_hook` looks like this:

```
"post_binary_hook": {
    "function": "TEENSY3_1Code.binary_hook",
    "toolchains": ["ARM_STD", "ARM_MICRO", "GCC_ARM"]
}
```

In this example, after the initial binary image for the target is generated, the build system will call the function `binary_hook` in the `TEENSY3_1Code` class. 

<span class="notes">**Note:** The definition of the `TEENSY3_1Code` class **must** exist in the *targets.py* file. </span>

Because `toolchains` is also specified, `binary_hook` will only be called if the toolchain used for compiling the code is either `ARM_STD`, `ARM_MICRO` or `GCC_ARM`. Note that specifying `toolchains` is optional: if it's not specified, the hook will be called no matter what toolchain is used.

As for the `binary_hook` code, this is how it looks in *targets.py*:

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

In this case, it converts the output file (`binf`) from binary format to Intel HEX format.

The hook code can look quite different for different targets. Take a look at the other classes in *targets.py* for more examples of hook code.

## device_name

This property is used to pass necessary data for exporting the mbed code to various third party tools and IDEs.

Please see [exporters.md](exporters.md) for information about this field.

