---
layout: post
title:  "Memory Mapped GPIO"
date:   2016-10-13 00:53:00 +0900
categories: artik tizen gpio
---

##### *by [Taehee Jeong](https://github.com/FredJeong)*{: style="color: #888"}

### GPIO on Samsung Exynos 5422

Though documentation or datasheet for Samsung Exynos 5422 CPU, which is used for ARTIK 10 and ODROID XU3/XU4 is not disclosed to public, following resources seen to be useful for figuring out low-level GPIO features.

[ODROID XU3 port regester map](http://odroid.com/dokuwiki/doku.php?id=en:xu3_gpio_register)
[Pin Programming (ARTIK Documentation)](https://developer.artik.io/documentation/developer-guide/gpio/kernel-gpio.html)

### GPIO On ARTIK 10

For ARTIK 10, I found following information for gpio pins and memory-mapped regesters.

* ARTIK GPIO pins are separeted into two classes, and each class has following base addresses
  1. gpio0(GPX0, GPX1) = `0x1340 0000`
  2. gpio3(GPA0, GPA1, GPA2, GPD0) = `0x1401 0000`
* Each ports (GPX0 ~ GPD0) has 8 pins at max, and each pins has one configuration(I/O) bit, and one value bit. **This is defferent from ODORID XU3**, documented above.
* When configuration register's address is **X**, (i.e. `0x 1340 0308` for *GPX1*), value register's address is **X+1** (`0x1340 0309`)
* For configuration bit, 0=input, 1=output. For value, 0=low, 1=high.


### Code for blinking an LED

Following code blinks an LED connected to gpio *pin 22* (*GPX1.6*, *J26[13]* on ARTIK 10 dev board). The code is adapted from ODROID wiki.

{% highlight c %}
/*
 * memory mapped gpio code for ARTIK 10
 * Source code adapted from http://odroid.com/dokuwiki/doku.php?id=en:xu3_gpio_register
 * modified by Taehee Jeong 13-10-2016
 */

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/mman.h>
#include <stdint.h>

static volatile uint32_t *gpio;

int main(int argc, char **argv)
{
    int fd ;

    if ((fd = open ("/dev/mem", O_RDWR | O_SYNC) ) < 0) {
        printf("Unable to open /dev/mem\n");
        return -1;
    }

    //gpio0 on ARTIK 10 has base address 0x13400000
    gpio = mmap(0, getpagesize(), PROT_READ | PROT_WRITE, MAP_SHARED, fd,
                                0x13400000);
    if (gpio < 0){
        printf("Mmap failed.\n");
        return -1;
    }

    // Print GPX2 configuration register.
    // GPX2 has offset 0x308
    printf("GPX2CON register : 0x%08x\n",
                *(unsigned int *)(gpio + 0x308));


    // Set direction of GPX2.7 configuration register as out.
    *(gpio + 0x308) |= (0x1 << 6);
    printf("GPX2CON register : 0x%08x\n",
                *(unsigned int *)(gpio + 0x308));
    int i;
    for (i = 0; i < 10; i++) {
    // GPX2.7 High
    *(gpio + 0x309) |= (1 << 6);
    printf("GPX2DAT register : 0x%08x\n",
                *(unsigned int *)(gpio + 0x309));
    sleep(1);
    // GPX2.7 Low
    *(gpio + 0x309) &= ~(1 << 6);
    printf("GPX2DAT register : 0x%08x\n",
                *(unsigned int *)(gpio + 0x309));
    sleep(1);
    }

    return 0;
}
{% endhighlight %}

Original code can be found [here](https://github.com/artik-snu/gpio/blob/master/main.c)

