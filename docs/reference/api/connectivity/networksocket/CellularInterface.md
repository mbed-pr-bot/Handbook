<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
<h2 id="cellular-api">Cellular</h2>

The CellularBase provides a C++ API for connecting to the internet over a Cellular device.

Arm Mbed OS provides a <a href="https://github.com/ARMmbed/mbed-os/tree/master/features/netsocket/cellular/generic_modem_driver" target="_blank">reference implementation of CellularBase</a>, which has more information.

### CellularBase class reference

[![View code](https://www.mbed.com/embed/?type=library)](https://os.mbed.com/docs/v5.6/mbed-os-api-doxy/class_cellular_base.html)

### Usage

To bring up the network interface:

1. Instantiate an implementation of the CellularBase class.
1. Call the `connect(pincode, apn)` function with a PIN code for your SIM card and an APN for your network.
1. Once connected, you can use Mbed OS <a href="/docs/v5.6/reference/network-socket.html" target="_blank">network sockets</a> as usual.

### Cellular example: connection establishment

This example establishes connection with the cellular network using Mbed OS CellularInterface.

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed_example/code/cellular-example/)](https://os.mbed.com/teams/mbed_example/code/cellular-example/file/fb873be06e31/main.cpp)

### Related content

- <a href="/docs/v5.6/reference/network-socket.html" target="_blank">Network socket</a> API reference overview.
