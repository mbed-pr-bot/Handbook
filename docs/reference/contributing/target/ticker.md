<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
### Microsecond ticker

The microsecond ticker is a system resource that many APIs use. The microsecond ticker needs a one microsecond resolution and uses a free-running hardware counter or timer with match register. Implement the API declared in `mbed-os\hal\us_ticker_api.h`.

At this point, we should be able to compile a handful of tests:

``mbed test -m BOARD_NAME --compile -t <toolchain>``

To execute the tests, you need to support <a href="https://github.com/armmbed/mbed-ls" target="_blank">`mbed-ls`</a>.
