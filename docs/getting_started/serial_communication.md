<span class="warnings">**Out of date**: This is not the most recent version of this page. Please see [the most recent version](y)</span>
# Board to PC communication over USB

The mbed microcontroller on your board can communicate with a host PC over the same USB cable that you use for programming.

<span class="tips">If you're working on Windows ealier than Windows 10, you might need to [install a serial driver](what_need.md#windows-serial-driver).</span>

This allows you to:

* Print out messages to a [host PC terminal (useful for debugging)](#terminal-applications).

* Read input from the host PC keyboard.

* Communicate with applications and programming languages running on the host PC that can communicate with a serial port. Examples are Perl, Python and Java.

## Hello World!

This program prints a "Hello World" message that you can view on a [terminal application](#terminal-applications). Communication over the USB serial port uses the standard serial interface. Specify the internal (USBTX, USBRX) pins to connect to the serial port routed over USB:


```c
#include "mbed.h"

Serial pc(USBTX, USBRX); // tx, rx

int main() {
    pc.printf("Hello World!\n");
    while(1);
}
```

## Terminal applications


Terminal applications run on your host PC. They provide a window where your mbed board can print and where you can type characters back to your board. 

<span class="tips">**Serial configuration:** The standard setup for the USB serial port is 9600 baud, 8 bits, 1 stop bit, no parity (9600-8-N-1)</span>

### Using terminal applications on Windows and OS X

#### Installing an application

There are many terminal applications for Windows, including:

* [CoolTerm](http://freeware.the-meiers.org/) - this is the application we use in this example. We use it often because it usually "just works".
* [Tera Term](http://sourceforge.jp/projects/ttssh2/files).
* [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/).
* Some Windows PCs come with **Hyperterminal** installed.

#### Configuring the connection

1. Plug in your mbed board.
1. Open CoolTerm.
1. Click **Connect**. This opens up an 8-n-1 9600 baud connection to the first available serial port. If you have more than one board plugged in, you may need to change the port under **Options > Serial Port > Port**.


Check your connection parameters:

1. Select **Options > Serial Port**.
1. You should see 9600 baud, 8 bits, 1 stop bit, no parity (9600-8-N-1).
1. If you do not see your board, click **Re-Scan Peripherals**.

Your terminal program is now configured and connected. 

### Using terminal applications on Linux

CoolTerm should work under Linux. If for some reason it doesn't, you can try one of the following:

* [Minicom](https://help.ubuntu.com/community/Minicom).
* [GNU Screen](https://www.gnu.org/software/screen/manual/screen.html).

## Additional examples

Use your terminal application to interact with the following examples.

If you're not sure how to build these examples and run them on your board, please see our [build tools section](../dev_tools/options.md).


### Echo back characters you type

```c
#include "mbed.h"

Serial pc(USBTX, USBRX);

int main() {
    pc.printf("Echoes back to the screen anything you type\n");
    while(1) {
        pc.putc(pc.getc());
    }
}
```


### Use the U and D keys to make LED1 brighter or dimmer

<span class="tips">**Note:** This example only works if LED1 is on the Pwm pin of the board you are using, such as the NUCLEO-F401RE. </span>

<span class="images">![](images/NUCLEOF401RE.png)<span>The pin map of the NUCLEO-F401RE shows LED1 on the Pwm pin.</span></span>

```c
#include "mbed.h"

Serial pc(USBTX, USBRX); // tx, rx
PwmOut led(LED1);

float brightness = 0.0;

int main() {
    pc.printf("Press U to turn LED1 brightness up, D to turn it down\n");

    while(1) {
        char c = pc.getc();
        if((c == 'u') && (brightness < 0.5)) {
            brightness += 0.01;
            led = brightness;
        }
        if((c == 'd') && (brightness > 0.0)) {
            brightness -= 0.01;
            led = brightness;
        }   
    }
}
```

### Pass characters in both directions

```c
#include "mbed.h"

Serial pc(USBTX, USBRX);
Serial uart(p28, p27);

DigitalOut pc_activity(LED1);
DigitalOut uart_activity(LED2);

int main() {
    while(1) {
        if(pc.readable()) {
            uart.putc(pc.getc());
            pc_activity = !pc_activity;
        }
        if(uart.readable()) {
            pc.putc(uart.getc());
            uart_activity = !uart_activity;
        }
    }
}
```
Tie pins together to see characters echoed back.

### Using stdin, stdout and stderr

By default, the C ``stdin``, ``stdout`` and ``stderr file`` handles map to the PC serial connection:

```c
#include "mbed.h"

int main() {
    printf("Hello World!\n");
    while(1);
}
```

### Read to a buffer

```c
#include "mbed.h"

DigitalOut myled(LED1);
Serial pc(USBTX, USBRX);

int main() {
    char c;
    char buffer[128];

    pc.gets(buffer, 4);
    pc.printf("I got '%s'\n", buffer);
    while(1);
}
```
