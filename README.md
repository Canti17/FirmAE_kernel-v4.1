Introduction
============

This contains the modified MIPS kernel for the FIRMADYNE framework, which
includes an in-tree `firmadyne` module to perform instrumentation and
emulation.

This module can be configured using the following parameters:

| Parameter | Default   | Values | Description |
| --------- | --------- | ------ | ----------- |
| devfs     | 1 (on)    | 0, 1   | Create stubs in devfs and emulate behavior |
| execute   | 1 (on)    | 0 - 5  | Counter to execute `/firmadyne/console` after 4th `execve()` syscall (requires syscall hooks), 0 to disable |
| reboot    | 1 (on)    | 0, 1   | Attempt to emulate system reboot by re-executing `/sbin/init` |
| procfs    | 1 (on)    | 0, 1   | Create stubs in procfs and emulate behavior |
| syscall   | 255 (all) | 0 - 16 | Output log bitmask for hooking system calls using the `kprobe` framework, 0 to disable |

Installing cross compilers
=====

For the cross-compilation, we use the gcc-5 cross-compilers. Execute the following commands:
>
	$ sudo add-apt-repository "deb http://cz.archive.ubuntu.com/ubuntu bionic main universe"
	$ sudo apt install gcc-5-mipsel-linux-gnu
	$ sudo apt install gcc-5-mips-linux-gnu
	$ sudo apt install gcc-5-arm-linux-gnueabihf
    $ sudo ln -s /usr/bin/mipsel-linux-gnu-gcc-5 /usr/bin/mipsel-linux-gnu-gcc
    $ sudo ln -s /usr/bin/mips-linux-gnu-gcc-5 /usr/bin/mips-linux-gnu-gcc
	$ sudo ln -s /usr/bin/mips-linux-gnu-gcc-5 /usr/bin/mips-linux-gnu-gcc

Usage
=====

The below instructions produce a MIPS Little-Endian (mipsel) kernel, a MIPS Big-Endian (mipseb) kernel and an ARM Little-Endian (armel) kernel.

MIPS Little-Endian (mipsel)
----------------------

Create the kernel build output directory:

`mkdir -p build/mipsel`

Copy the configuration file into the build directory:

`cp config.mipsel build/mipsel/.config`

Assuming that the appropriate cross-compiler is installed in `/usr/bin/mipsel-linux-gnu-gcc`, execute:

`make ARCH=mips CROSS_COMPILE=mipsel-linux-gnu- O=./build/mipsel -j8`

The output kernel image will be generated at the following location:

`build/mipsel/vmlinux`

MIPS Big-Endian (mipseb)
-------------------

Create the kernel build output directory:

`mkdir -p build/mipseb`

Copy the configuration file into the build directory:

`cp config.mipseb build/mipseb/.config`

Assuming that the appropriate cross-compiler is installed in `/usr/bin/mips-linux-gnu-gcc`, execute:


`make ARCH=mips CROSS_COMPILE=mips-linux-gnu- O=./build/mipseb -j8`

The output kernel image will be generated at the following location:

`build/mipseb/vmlinux`

ARM Little-Endian (armel)
----------------------

Create the kernel build output directory:

`mkdir -p build/armel`

Copy the configuration file into the build directory:

`cp config.armel build/armel/.config`

Assuming that the appropriate cross-compiler is installed in `/usr/bin/arm-linux-gnueabihf-gcc`, execute:

`make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- O=./build/armel -j8`

or (for the zImage format):

`make zImage ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- O=./build/armel -j8`

The output kernel image will be generated at the following location:

`build/armel/vmlinux`

Notes
=====

This instrumented MIPS kernel is built for the `ARCH_MALTA`
[MIPS Malta](http://wiki.qemu.org/download/qemu-doc.html#MIPS-System-emulator)
target with a 24kf processor. As a result, hardware support on this
emulated target is limited to peripherals that are both available on the
emulated target and supported by QEMU. Since an emulated PCI bus is available
and supported, this allows additional ethernet devices (e.g. `rtl8139`, 
`smc91c111`, `pcnet32`, etc.) to be attached to the virtualized system. 
Emulated hard drives can be attached using the IDE block device interface.

As future work, it may be useful to switch to
[VirtIO](http://wiki.libvirt.org/page/Virtio) on MIPS, since support has been
recently merged into the Linux kernel. However, this would require a kernel
upgrade. Additionally, it may be useful to add support for MIPS64 systems,
although these do not appear to be prevalent. Nevertheless, at the time, we
performed our published experiments over our dataset using this kernel for
MIPS systems.

Pull requests are greatly appreciated!
