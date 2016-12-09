---
layout: post
title:  "Application Build Using gcc"
date:   2016-10-13 00:34:00 +0900
categories: artik tizen
---

##### *by [Taehee Jeong](https://github.com/FredJeong)*{: style="color: #888"}

### Build attempts

In order to compile and test some applications on ARTIK environment, several ways were tried:

#### Using gbs (git build system)

Using gbs is ideal way to bild applications for Tizen, and I followed [this instruction](https://blogs.s-osg.org/introduction-tizen-development-artik/) by Phil Cova.
But currently buildding attempts were failed due to following errors.

```
\> gbs build -P "tizen_common_3.0b_artik_armv7l" --arch armv7l --include-all
info: generate repositories ...
warning: No local package repository for arch armv7l
info: build conf has been downloaded at:
      /var/tmp/wwee3631-gbs/tizen_common_3.0b_artik_armv7l.conf
Can't locate Build.pm in @INC (you may need to install the Build module)
(@INC contains: /usr/lib/build /usr/bin /usr/bin/build /etc/perl
/usr/local/lib/perl/5.18.2 /usr/local/share/perl/5.18.2 /usr/lib/perl5
/usr/share/perl5 /usr/lib/perl/5.18 /usr/share/perl/5.18
/usr/local/lib/site_perl .) at /usr/bin/depanneur line 66.
BEGIN failed--compilation aborted at /usr/bin/depanneur line 66.
error: <gbs>some packages failed to be built
```

After installed Module::Build, (I think it wasn't right one)

```
\> gbs build -P "tizen_common_3.0b_artik_armv7l" --arch armv7l --include-all
info: generate repositories ...
warning: No local package repository for arch armv7l
info: build conf has been downloaded at:
      /var/tmp/wwee3631-gbs/tizen_common_3.0b_artik_armv7l.conf
Can't locate Build/Rpm.pm in @INC (you may need to install the Build::Rpm
module) (@INC contains: /usr/lib/build /usr/bin /usr/bin/build /etc/perl
/usr/local/lib/perl/5.18.2 /usr/local/share/perl/5.18.2 /usr/lib/perl5
/usr/share/perl5 /usr/lib/perl/5.18 /usr/share/perl/5.18
/usr/local/lib/site_perl .) at /usr/bin/depanneur line 67.
BEGIN failed--compilation aborted at /usr/bin/depanneur line 67.
error: <gbs>some packages failed to be built
```

Further investigation to this problem should be done, since gbs is necessary tool for building apps properly.

#### Using Tizen studio

Tizen Studio provides awesome SDK for build native/web applications for Tizen, but currently it does not support Tizen 3.0 officially. Since our development cycle did not get to the whole user application development level, development using Tizen SDK can be postponed.

Building full apps with Tizen Studio will be tried after we complete gpio library development.

#### Using gcc

First atttempt to use gcc by simply doing `arm-linux-gnueabi-gcc -o test test.c` did not work, even though include path for c libraries were specified. Then I found that files from the kernel code should be included, too.

That finding resulted in writing a Makefile for gcc build of simple Tizen native application.
Even though gcc was from Tizen 2.4 SDK, it worked well.

And then resulting executable file is copied to ARTIK via USB flash memory, because I couldn't use internet connection. Luckily mounting USB flash memory to Tizen was simple task, being exactly same with other linux distributions.
    

```makefile
SDKDIR=${HOME}/tizen-studio
DEVICE_CORE=${SDKDIR}/platforms/tizen-2.4/mobile/rootstraps/mobile-2.4-device.core/

LINK=-L${DEVICE_CORE}/usr/lib -L${DEVICE_CORE}/lib

INCLUDE=-I${DEVICE_CORE}/usr/include

# EDIT KERNEL CODE PATH
INCLUDE+=-I${KERNEL_ROOT}/linux-exynos/include

SYSROOT=${DEVICE_CORE}

GCCROOT=${SDKDIR}/tools/arm-linux-gnueabi-gcc-4.9/bin

CFLAGS=$(INCLUDE) $(LINK) --sysroot=$(SYSROOT) -lcapi-system-sensor

CC=$(GCCROOT)/arm-linux-gnueabi-gcc

all: test

test: main.c
    @echo [Arm-cc] $<...
    @$(CC) $(CFLAGS) $< -o $@

clean:
    @rm test
```


