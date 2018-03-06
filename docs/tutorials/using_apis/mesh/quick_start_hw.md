<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
### Configuring the hardware

#### Selecting your radio

6LoWPAN network uses IEEE 802.15.4 radios and therefore, operates on one of the following unlicensed
frequency bands:

- 868.0–868.6 MHz: Europe, allows one communication channel (2003, 2006, 2011[4])
- 902–928 MHz: North America, up to ten channels (2003), extended to thirty (2006)
- 2400–2483.5 MHz: worldwide use, up to sixteen channels (2003, 2006)

The data rate varies from 20 kbit/s to 250 kbit/s. The data rate available per node should be considered when designing the application. Basically, the data rate is divided between all nodes in the network. Roughly half of the channel capacity should be allocated for signalling purposes, and each hop requires retransmisson of the packet.

![Datarate](https://s3-us-west-2.amazonaws.com/mbed-os-docs-images/bw.png)

<span class="tips">**Rule of thumb:** The bandwidth per node is divided by the number of nodes in the network and the number of hops.</span>

#### Notes on different hardware

As the stack runs on multiple different Mbed OS development boards there might be combinations of board and RF shields that may, or may not, work together due to pin collision or other reasons.

This page aims to collect information regarding different hardware combinations.

Please see <a href="https://github.com/ARMmbed/mbed-os-example-mesh-minimal/blob/master/Hardware.md" target="_blank">Notes on different hardware</a> on Mesh example application for up to date copy of this list.

#### RF shields

Following RF shield have been used with Mbed OS mesh examples.

- <a href="https://firefly-iot.com/product/firefly-arduino-shield-2-4ghz/" target="_blank">FIREFLY 6LOWPAN ARDUINO SHIELD</a>.
- <a href="http://www.nxp.com/products/software-and-tools/hardware-development-tools/freedom-development-boards/freedom-development-board-for-mcr20a-wireless-transceiver:FRDM-CR20A" target="_blank">Freedom Development Board for MCR20A</a>.
- <a href="http://www.st.com/content/st_com/en/products/ecosystems/stm32-open-development-environment/stm32-nucleo-expansion-boards/stm32-ode-connect-hw/x-nucleo-ids01a4.html" target="_blank">X-NUCLEO-IDS01A4</a>.
- <a href="https://os.mbed.com/platforms/NCS36510/" target="_blank">DVK-NCS36510-MBED-GEVB</a> Development board, contains internal RF chip.

#### Tested development boards

The following table shows which development boards have been tested. It does not present our current testing infrastructure, so we cannot guarantee all combinations but we do our best to ensure it is up to date.

| board / RF shield | Atmel | MCR20A | X-NUCLEO-IDS01A4 |
|-------------------|-------|-----|------------------|
| FRDM-K64F | <span style='background-color: #5f5;'>Yes</span> | <span style='background-color: #5f5;'>Yes</span> | |
| <span style='background-color: #ff5;'>NUCLEO_F429ZI **(1)**</span> | <span style='background-color: #5f5;'>Yes</span> | <span style='background-color: #5f5;'>Yes</span> | <span style='background-color: #ff5;'>Modified, **(3)**</span> |
| NUCLEO_F401RE | <span style='background-color: #5f5;'>Yes</span> | | |
| ublox EVK-ODIN-W2 | <span style='background-color: #5f5;'>Yes</span> | <span style='background-color: #f00;'>No. **(2)**</span> | |
| Onsemi NCS36510 <span style='background-color: #5f5;'>(internal RF)</span> | | | |
| NXP KW24D <span style='background-color: #5f5;'>(internal RF)</span> | | <span style='background-color: #f00;'>Yes **(4)**</span> | |


**Notes:**

1. If the Ethernet driver is enabled, requires HW modifications if RF shield uses SPI1. See <a href="https://github.com/ARMmbed/sal-nanostack-driver-stm32-eth" target="_blank">Driver notes</a> and <a href="https://github.com/ARMmbed/nanostack-border-router-private/issues/17" target="_blank">nanostack-borderrouter-private Issue #17</a>.
2. Pin collision, see <a href="https://github.com/ARMmbed/mbed-os-example-mesh-minimal/issues/55" target="_blank">mesh-minimal Issue 55</a>.
3. X-NUCLEO-IDS01A4 expansion board required modifications to be used in Mbed OS. See <a href="https://github.com/ARMmbed/stm-spirit1-rf-driver" target="_blank">Driver readme</a>.
4. KW24D has MCR20A chip integrated. Use the same driver.
