<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
## HeartRateService

People practicing physical activities use heart rate monitors to track their pulse in real time and improve their physical performances.

The <a href="https://www.bluetooth.org/docman/handlers/downloaddoc.ashx?doc_id=239866" target="_blank">Bluetooth Heart Rate Service</a> defines how data from a heart rate sensor should be exposed through a BLE link. The standard nature of the service allows seamless operations between collectors - usually smartphone applications - and heart rate monitors conforming to the service.

The HeartRateService class implements the Bluetooth Heart Rate service as defined by the Bluetooth body. Makers of BLE enabled fitness devices can use it to expose interoperably heart rate sensor data.

<span class="note"> **Note:** The Bluetooth Heart Rate Service is part of the <a href="https://www.bluetooth.org/docman/handlers/downloaddoc.ashx?doc_id=239865" target="_blank">Bluetooth Heart Rate Profile</a>, which defines behaviors that a Bluetooth heart rate sensor expects. You must ensure that your application conforms to the heart rate profile to guarantee interoperability of your heart rate sensors.</span>

### HeartRateService example

[![View code](https://www.mbed.com/embed/?url=https://os.mbed.com/teams/mbed-os-examples/code/mbed-os-example-ble-HeartRate/)](https://os.mbed.com/teams/mbed-os-examples/code/mbed-os-example-ble-HeartRate/file/307bde0f868f/source/main.cpp)

### Related content

- [Bluetooth Heart Rate Service](https://www.bluetooth.org/docman/handlers/downloaddoc.ashx?doc_id=239866) specification.
- [Bluetooth Heart Rate Profile](https://www.bluetooth.org/docman/handlers/downloaddoc.ashx?doc_id=239865) specification.
