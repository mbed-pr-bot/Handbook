<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## I2C

The I2C interface provides I2C Master functionality. I2C is a two wire serial protocol that allows an I2C Master to exchange data with an I2C Slave. You can use it to communicate with I2C devices such as serial memories, sensors and other modules or integrated circuits.

The I2C protocol supports up to 127 devices per bus, and its default clock frequency is 100KHz.

<span class="warnings">**Warning:** Remember that you will need a pull-up resistor on sda and scl.</br>
All drivers on the I2C bus are required to be open collector, and so it is necessary to use pull-up resistors on the two signals. A typical value for the pull-up resistors is around 2.2k ohms, connected between the pin and 3v3. </span>

### I2C class reference

<span class="notes">**Note:** The Arm Mbed API uses 8 bit addresses, so make sure to left-shift 7 bit addresses by 1 bit before passing them. </span>

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/classmbed_1_1_i2_c.html)

### I2C hello, world

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/I2C_HelloWorld/)](https://os.mbed.com/teams/mbed_example/code/I2C_HelloWorld/file/fa13d56ff9ff/main.cpp)
