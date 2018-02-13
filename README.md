# Simple_OS
Tutorial on how to create you own very simple Operating System
I have created an indepth detailed instructions on how to install the packages needed.
Hopefully, this simplifies the process so that you may be on your journey to further learn on how and OS works at the most basic, simplistic of levels, if there is such a thing. 


This tutorial follows:
[cfenollosa/os-tutorial](https://github.com/cfenollosa/os-tutorial)
Also special thanks to my mentor Swati Gupta [github/guptaNswati](https://github.com/guptaNswati)
Who guided me along through my bumps in creating my very first simple OS.

#Install and Launch Your Virtual Maching Ubuntu Linux Environment
* Open a terminal and run vagrant box list will print the list of boxes available on the computers.
`vagrant box list`
* #1
`vagrant init`
* #2
Open `Vagrantfile` and replace where is says `base` with `ubuntu/trusty64` as we are running Ubuntu in this VM.
Save file
* #3
Run `vagrant up`
* #4
Run `vagrant ssh`
* You are in your Virtual Machine!!!
* More Info:
* [VM wiki](https://en.wikipedia.org/wiki/Virtual_machine)
 
#Install QEMU with Nasm
* QEMU is a generic and open source machine emulator and virtualizer.
* The Netwide Assembler (NASM) is an assembler and disassembler for the Intel x86 architecture. It can be used to write 16-bit, 32-bit (IA-32) and 64-bit (x86-64) programs. NASM is considered to be one of the most popular assemblers for Linux.
* `sudo apt-get install build-essential qemu nasm`
* [QEMU](https://www.qemu.org/download/)

###Prepare the Build
The GNU Compiler Collection is an advanced piece of software with dependencies. You need to install certain dependencies in order to build gcc. 
* installing gmp
`sudo apt-get install libgmp3-dev`
* installing mpfr
`sudo apt-get install libmpfr-dev libmpfr-doc libmpfr4 libmpfr4-db`   
* installing libmpc
`sudo apt-get install libmpc-dev`
* installing Make
`sudo apt-get install make`
* installing Texinfo
`sudo apt-get install Texinfo`
* installing Flex
`sudo apt-get install Flex`
* installing Bison
`sudo apt-get install Bison`
* More Info:
* [GCC-CrossCompiler](https://wiki.osdev.org/GCC_Cross_Compiler)

#Build binutils and a cross-compiled gcc, and we will put them into /usr/local/i386elfgcc, so let's export some paths now. Feel free to change them to your liking.
* `export PREFIX="/usr/local/cross-compiler"`
* `export TARGET=i686-elf`
* `export PATH="$PREFIX/bin:$PATH"`

#binutils
* `mkdir /tmp/src`
* `cd /tmp/src`
* `curl -O http://ftp.gnu.org/gnu/binutils/binutils-2.30.tar.gz`
* More Info:
* [GNU ORG](https://ftp.gnu.org/gnu/binutils/)

* `tar xf binutils-2.30.tar.gz`
* `mkdir binutils-build`
* `cd binutils-build`
* `../binutils-2.24/configure --target=$TARGET --enable-interwork --enable-multilib --disable-nls --disable-werror --prefix=$PREFIX 2>&1 | tee configure.log`
* `sudo make all install 2>&1 | tee make.log`

#If the peak RAM used during installation is greater than that of EC2 micro instance. Use a larger instance or use swap. If you are using VM like myself you will need this step before proceeding with gcc.

* `SWAP=/tmp/swap`
* `dd if=/dev/zero of=$SWAP bs=1M count=500`
* `mkswap $SWAP`
* `sudo swapon $SWAP`
* More Info:
* [StackOverFlow/Error](https://stackoverflow.com/questions/18389612/make-exits-with-error-2-when-trying-to-install-gcc-4-8-1)

#cross-compiler gcc
* `cd /tmp/src`
* `curl -O https://ftp.gnu.org/gnu/gcc/gcc-4.9.1/gcc-4.9.1.tar.bz2`
* `tar xvfj gcc-4.9.1.tar.bz2`
* `mkdir gcc-build`
* `cd gcc-build`
* `../gcc-4.9.1/configure --target=$TARGET --prefix="$PREFIX" --disable-nls --disable-libssp --enable-languages=c --without-headers`
* `sudo make all-gcc`
* `sudo make all-target-libgcc`
* `sudo make install-gcc`
* `sudo make install-target-libgcc`

# check GNU binutils and the compiler
* `ls /usr/local/cross-compiler/bin`, prefixed by i686-elf-

# compile system independent kernel.c 
* `i686-elf-gcc -ffreestanding -c kernel.c -o kernel.o`

# compile kernel_start
* `nasm kernel_start.asm -f elf -o kernel_start.o`

# link both obj files to make a binary and place kernel at 0x1000 location
* `i686-elf-ld -o kernel.bin -Ttext 0x1000 kernel_start.o kernel.o --oformat binary`

# compile bootsect
* `nasm bootsector.asm -f bin -o bootsector.bin`

# create one kernel image joining both bin with bootsect at head
* `cat bootsector.bin kernel.bin > kernel-image.bin`

# to run on vm, create a flp image 
#directly copy kernel.bin to the first sector of the floppy disk image
* `dd status=noxfer conv=notrunc if=kernel-image.bin of=disk.flp`

# run and use -curses to bypass sdl restrictions
* `qemu-system-x86_46 -curses -fda kernel-image.bin`

# to exit
* `alt+2`, enter quit

# cleanup
* `rm *.bin *.o *.img *.flp *.iso`
* `rm -rf cdromiso`
