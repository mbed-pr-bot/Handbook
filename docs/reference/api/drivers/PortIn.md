<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## PortIn

Use the PortIn interface to read an underlying GPIO port as one value. This is much faster than <a href="/docs/v5.6/reference/busin.html" target="_blank">BusIn</a> because you can read a port in one go, but it is much less flexible because you are constrained by the port and bit layout of the underlying GPIO ports.

A mask can be supplied so only certain bits of a port are used, allowing other bits to be used for other interfaces.

### PortIn class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/classmbed_1_1_port_in.html)

### PortIn hello, world

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/users/mbed_official/code/PortIn_HelloWorld/)](https://os.mbed.com/users/mbed_official/code/PortIn_HelloWorld/file/92064442fd12/main.cpp)

### Related content

- <a href="/docs/v5.6/reference/busin.html" target="_blank">BusIn</a> API reference.
