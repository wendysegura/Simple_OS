# Simple_OS
Tutorial on how to create you own very simple Operating System
I have created an indepth detailed instructions on how to install the packages needed.
Hopefully, this simplifies the process so that you may be on your journey to further learn on how and OS works at the most basic, simplistic of levels, if there is such a thing. 


# Acknowledgements:
[cfenollosa/os-tutorial](https://github.com/cfenollosa/os-tutorial)
Also special thanks to my mentor Swati Gupta [github/guptaNswati](https://github.com/guptaNswati)
Who guided me along through my bumps in creating my very first simple OS.

Pre-requisites
--------------
* VM running on Ubuntu
* nasm
* qemu
* bin-utils
* cross-compiler gcc
* floppy disk image
* gmp, mpfr, libmpc
* make
* texinfo
* flex
* bison

Process
--------
* bootsector
* 16 bit mode
* 32 bit mode
* Kernel
* Print to screen

Run
----
* create a floppy disk image and copy boot sector code to it.
* `dd status=noxfer conv=notrunc if=kernel-image.bin of=disk.flp`
* `qemu-system-x86_64 -curses -fda kernel-image.bin`

Demo
----
