<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## SocketAddress

Use the SocketAddress class to represent the IP address and port pair of a unique network endpoint. Most network functions are also overloaded to accept string representations of IP addresses, but you can use SocketAddress to avoid the overhead of parsing IP addresses during repeated network transactions, and you can pass it around as a first class object.

### SocketAddress class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/class_socket_address.html)

### SocketAddress Example

Here is an example to read current UTC time. This example uses the SocketAddress class to get the server IP address and port.

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/mbed-os-example-udp-sockets/)](https://os.mbed.com/teams/mbed_example/code/mbed-os-example-udp-sockets/file/cf516d904427/main.cpp)
