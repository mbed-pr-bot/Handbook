<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## BusOut

Use the BusOut interface to create a number of <a href="/docs/v5.6/reference/digitalout.html" target="_blank">DigitalOut</a> pins that you can write as one value. This API is useful for writing multiple outputs at once. You can use this API to write clearer code faster.

You can use any of the numbered Arm Mbed pins as a DigitalOut in the BusOut.

**Tips:**

- You can have up to 16 pins in a Bus.
- The order of pins in the constructor is the reverse order of the pins in the byte order. So if you have BusOut(a,b,c,d,e,f,g,h) then the order of bits in the byte would be `hgfedcba` with `a` being bit 0, `b` being bit 1, `c` being bit 2 and so on.

### BusOut class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/classmbed_1_1_bus_out.html)

### BusOut Hello World!

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/BusOut_HelloWorld/)](https://os.mbed.com/teams/mbed_example/code/BusOut_HelloWorld/file/6337070122f8/main.cpp)

### Related content

- <a href="/docs/v5.6/reference/digitalout.html" target="_blank">DigitalOut</a> API reference.
