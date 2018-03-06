<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## UDPSocket

The UDPSocket class provides the ability to send packets of data over UDP, using the `sendto` and `recvfrom` member functions. Packets can be lost or arrive out of order, so we suggest using a <a href="/docs/v5.6/reference/tcpsocket.html" target="_blank">TCPSocket</a> when you require guaranteed delivery.

The constructor takes in the NetworkStack pointer to open the socket on the specified NetworkInterface. If you do not pass in the constructor, then you must call `open` to initialize the socket.

### UDPSocket class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/class_u_d_p_socket.html)

### UDPSocket Example

Here is a UDP example to read the current UTC time by sending a request to the NIST Internet Time Service. 

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/mbed-os-example-udp-sockets)](https://os.mbed.com/teams/mbed_example/code/mbed-os-example-udp-sockets/file/cf516d904427/main.cpp)

### Related content

- <a href="/docs/v5.6/reference/tcpsocket.html" target="_blank">TCPSocket</a> API reference.
