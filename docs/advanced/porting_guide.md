<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
# Porting guide: adding target support to mbed OS 5

There are two typical ways to add target support to mbed OS: Either you'll be adding a [completely new microcontroller](#adding-a-new-microcontroller) and board, or you'll be adding a new board that contains an [already-supported microcontroller](#adding-a-new-board-or-module). 

There is a dependency on CMSIS-CORE and CMSIS-Pack, so make sure the microcontroller already has these available.

## Adding a new microcontroller

Add the empty target implementation reference to the working directory using: 

```
mbed import mbed-os-example-new-target
cd mbed-os-example-new-target\mbed-os
git checkout master
git pull
git checkout -b my-new-target
git remote set-url origin https://github.com/USERNAME/mbed-os
cd ..
```

### Target description

Add the target description to ```mbed-os\targets\targets.json```:

``` json
"MCU_NAME": {
    "inherits": ["Target"],
    "core": "Cortex-M3",
    "supported_toolchains": ["ARM", "GCC_ARM", "IAR"],
    "extra_labels": ["VENDOR", "MCU_FAMILY", "MCU_NAME"],
    "macros": [],
    "device_has": ["SERIAL", "STDIO_MESSAGES"]
},
"BOARD_NAME": {
    "inherits": ["MCU_NAME"],
    "macros_add": []
}
```

### Bootstrap code

You need a number of files to first successfully compile. You need CMSIS-CORE files for startup and peripheral memory addresses as well as the linker scripts for ARM, IAR and GCC toolchains. These files are usually in the ```mbed-os\targets\TARGET_VENDOR\TARGET_MCU_FAMILY\TARGET_MCUNAME\device``` directory.

If the microcontroller has a SYS_TICK, the RTOS will configure this, so only a few macros are needed in ```mbed-os\targets\TARGET_VENDOR\mbed-rtx.h```.

Dynamic vector relocation also requires some extensions to CMSIS-CORE. ```mbed-os\targets\TARGET_VENDOR\TARGET_MCUNAME\cmsis.h``` is the entry point to the target for mbed OS software. This will include the vector relocation additions and device-specific headers that include CMSIS-CORE. A relocation routine is needed in ```mbed-os\targets\TARGET_VENDOR\TARGET_MCUNAME\cmsis_nvic.c and .h```

Now verify your target is identified by using ```mbed compile -m MCU_NAME -t <toolchain>```. This compiles.

The next step is to enable the test harness dependencies. To run the test suite, there is a minimum requirement of GPIO, microsecond ticker and serial implementation, all explained below.

### GPIO

The ```gpio_s``` is for referencing memory-mapped GPIO registers and passing related pin information or other IO operation data that the HAL needs. These structures are defined in ```objects.h```. The required implementation definition is in ```mbed-os\hal\gpio_api.h```.

### Serial

The ```serial_s``` is for referencing memory-mapped serial registers and passing related pin and peripheral operation information data that the HAL needs. These structures are defined in ```objects.h```. The required implementation definition is in ```mbed-os\hal\serial_api.h```.

### Microsecond ticker

The microsecond ticker is a system resource that many APIs and other timing utilities use. It needs a one microsecond resolution and should be implemented using a free-running hardware counter or timer with match register. The required implementation definition is in ```mbed-os\hal\us_ticker_api.h```.

At this point, we should be able to compile a handful of  tests: 

``mbed test -m BOARD_NAME --compile -t <toolchain>``

To execute the tests, you'll need to already support [mbed-ls](https://github.com/armmbed/mbed-ls).

### All the others

At this point, the HAL structure should be familiar as a programming model. There are many more APIs to implement, which you enable by adding a ```device_has``` attribute to the MCU_NAME and then providing the implementation. Other HAL APIs include but are not limited to:

```
device_has       |   api
-----------------|------------------
ANALOGIN         |   analog_in.h
ANALOGOUT        |   analog_out.h
CAN              |   can_api.h
EMAC             |   emac_api.h
INTERRUPTIN      |   gpio_irq_api.h
I2C I2CSLAVE     |   i2c_api.h
LOWPOWERTIMER    |   lp_ticker_api.h
PORT_IN PORT_OUT |   port_api.h
PWMOUT           |   pwmout_api.h
RTC              |   rtc_api.h
SLEEP            |   sleep_api.h
SPI SPISLAVE     |   spi_api.h
TRNG             |  trng_api.h
```
