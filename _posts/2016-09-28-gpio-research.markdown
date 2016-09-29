---
layout: post
title:  "GPIO Research"
date:   2016-09-30 01:28:00 +0900
categories: artik tizen
---

*by [Taehee Jeong](https://github.com/FredJeong)*

The first objective of this project is to develop GPIO API.

From the [ARTIK Documentation](https://developer.artik.io/documentation/tutorials/using-gpio-on-artik-10.html), `sysfs` seems a good point to start.

After brief search on the internet, all the GPIO pins in linux environment seem to be stored on `/sys/class/gpio`. Basic example for opening and reading/writing GPIO is as followed:

{% highlight bash %}
echo 22 > /sys/class/gpio/export
#/sys/class/gpio/gpio22 has been created
echo out > /sys/class/gpio/direction

echo 1 > /sys/class/gpio/direction
echo 0 > /sys/class/gpio/direction
{% endhighlight %}

I succesfully turned LED on and off

#TL;DR
[Exploring ARTIK GPIO](https://developer.artik.io/documentation/tutorials/using-gpio-on-artik-10.html) from ARTIK Documentation
[Pin Programming](https://developer.artik.io/documentation/developer-guide/gpio/kernel-gpio.html) from ARTIK Documentation
