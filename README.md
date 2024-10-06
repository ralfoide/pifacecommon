pifacecommon
============

Common functions for interacting with PiFace products.


Fork Documentation
==================

This is a fork of https://github.com/piface/pifacecommon to make it work on a Raspberry Pi 4 with
Raspberry Pi OS based on Debian Bookworm and a Linux Kernel 6.x.

### TL;DR

The only difference is the GPIO #25 number in `interrupts.py`, which becomes 537 (512 + 25).

### Longer version: 

The Linux Kernel has changed the way GPIOs are being addressed -- since circa 2015 the "sysfs"
interface has been slowly deprecated, to be entirely replaced by 2020.
`interrupts.py` makes an access to `/sys/class/gpio/gpio25/` which does not exist in the recent
kernel gpio driver.

However, the feature has not been entirely removed.
It still works, with a little caveat: the sysfs interface now explicitly lists each gpio chip, with
a "base number" and a number of GPIOs handled.
A user-level tool `gpioinfo` lists all the pins.

In our case, we want `gpiochip0` which is defined as:

    /sys/class/gpio/gpiochip512/base = 512
    /sys/class/gpio/gpiochip512/ngpio = 58

There are ways to iterate all the pins. There's even a GPIO module for Python.
However on the RPi 4, we know the legacy GPIO25 maps to offset #25 in the `gpiochip0` with a base
of 512.
Thus, the new pin number is 512 + 25 = 537.



Original Documentation (obsolete)
=================================

[http://pifacecommon.readthedocs.org/](http://pifacecommon.readthedocs.org/)

You can also find the documentation installed at:

    /usr/share/doc/python3-pifacecommon/


Install
=======

On a Raspberry Pi 4 with Debian Bookworm, the old recommendation from using `pip3 install pifacecommon`
is now obsolete.

The modern recommendation is to install PIP modules in a Python Virtual Environment, to avoid
breaking APT-based system libraries.

Install the forked version of `pifacecommon` like this:

    # Create a new Python Virtual Environment
    $ python3 -m venv my_piface
    $ cd my_piface

    # Install PiFaceCommon from a locally checked out source
    $ git clone https://github.com/ralfoide/pifacecommon.git
    $ bin/pip3 install pifacecommon/

    # Install PiFaceDigitalio from the repository
    $ bin/pip3 install pifacedigitalio

    # Try one of the examples
    $ git clone https://github.com/piface/pifacedigitalio.git
    $ bin/python3 pifacedigitalio/examples/presslights.py

Note: 
Allegedly, you can still install PIP modules globally if you pass the aptly-named argument
`--break-system-packages` to `pip`.

You will also need to set up automatic loading of the SPI kernel module which
can be done with the latest version of `raspi-config`. Run:

    $ sudo raspi-config

Then navigate to `Advanced Options`, `SPI` and select `yes`.

~~
