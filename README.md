stm32f429-linux-builder
======================
This is a simple tool designed to create a uClinux distribution for STM32f429
Discovery board from [STMicroelectronics](http://www.st.com/). STM32F429 MCU
offers the performance of ARM Cortex M4 core (with floating point unit) running
at 180 MHz while reaching reasonably lower static power consumption.


Prerequisites
=============
The builder requires that various tools and packages be available for use in
the build procedure:

* Set ARM/uClinux Toolchain:
  - Set path for tool-chain
```
    echo "PATH=`pwd`/tool-chain/arm-2010q1/bin:$PATH" >> ~/.bashrc
    source ~/.bashrc
```
* Install [genromfs](http://romfs.sourceforge.net/) & other package for X86-64
```
    sudo apt-get install -y libc6-i386 libc6:i386 libncurses5:i386 libstdc++6:i386 genromfs
```


Build Instructions
==================
* Simply execute ``make``, and it will fetch and build u-boot, linux kernel, and busybox from scratch:
```
    make
```
* Using ST-LINK, upload next binary images :
```
    uboot_target = $(target_out)/uboot/u-boot.bin  ==> 0x08000000
    kernel_target = $(target_out)/kernel/arch/arm/boot/xipuImage.bin ==> 0x08020000
    rootfs_target = $(target_out)/romfs.bin ==> 0x08120000
```
Be patient when ST-LINK is flashing. Typically, it takes about 55 seconds.
Use `make help` to get detailed build targets.


USART Connection
================
The STM32F429 Discovery is equipped with various USARTs. USART stands for
Universal Synchronous Asynchronous Receiver Transmitter. The USARTs on the
STM32F429 support a wide range of serial protocols, the usual asynchronous
ones, plus things like IrDA, SPI etc. Since the STM32 works on 3.3V levels,
a level shifting component is needed to connect the USART of the STM32F429 to
a PC serial port.

Most PCs today come without an native RS232 port, thus an USB to serial
converter is also needed.

For example, we can simply connect the RX of the STM32 USART3 to the TX of
the converter, and the TX of the USART3 to the RX of the converter:
* pin PC10 -> TXD
* pin PC11 -> RXD


Reference Boot Messages
=======================
```
U-Boot 2010.03-00003-g934021a-dirty (Jan 02 2017 - 10:42:47)

CPU  : STM32F4 (Cortex-M4)
Freqs: SYSCLK=180MHz,HCLK=180MHz,PCLK1=45MHz,PCLK2=90MHz
Board: STM32F429I-DISCOVERY board,Rev 1.0
DRAM:   8 MB
Using default environment

Hit any key to stop autoboot:  0
Starting kernel ...

Linux version 2.6.33-arm1 (inpyo@inpyo-ubuntu) (gcc version 4.4.1 (Sourcery G++ Lite 2010q1-189) ) #1 Fri Dec 30 09:36:52 KST 2016
CPU: ARMv7-M Processor [410fc241] revision 1 (ARMv7M)
CPU: NO data cache, NO instruction cache
Machine: STMicro STM32
Ignoring unrecognised tag 0x54410008
Built 1 zonelists in Zone order, mobility grouping off.  Total pages: 1778
Kernel command line: stm32_platform=stm32429-disco mem=7M console=ttyS2,115200n8 consoleblank=0 root=/dev/mtdblock0 rdinit=/sbin/init video=vfb:enable,fbmem:0x90700000,fbsize:0x100000
PID hash table entries: 32 (order: -5, 128 bytes)
Dentry cache hash table entries: 1024 (order: 0, 4096 bytes)
Inode-cache hash table entries: 1024 (order: 0, 4096 bytes)
Memory: 7MB = 7MB total
Memory: 6976k/6976k available, 192k reserved, 0K highmem
Virtual kernel memory layout:
    vector  : 0x00000000 - 0x00001000   (   4 kB)
    fixmap  : 0xfff00000 - 0xfffe0000   ( 896 kB)
    vmalloc : 0x00000000 - 0xffffffff   (4095 MB)
    lowmem  : 0x90000000 - 0x90700000   (   7 MB)
    modules : 0x90000000 - 0x90800000   (   8 MB)
      .init : 0x9000a000 - 0x9000e000   (  16 kB)
      .text : 0x08028000 - 0x080c4000   ( 624 kB)
      .data : 0x90008000 - 0x90018460   (  66 kB)
Hierarchical RCU implementation.
NR_IRQS:90
Console: colour dummy device 80x30
Calibrating delay loop... 168.34 BogoMIPS (lpj=841728)
Mount-cache hash table entries: 512
bio: create slab <bio-0> at 0
Switching to clocksource cm3-systick
ROMFS MTD (C) 2007 Red Hat, Inc.
io scheduler noop registered
io scheduler deadline registered (default)
Console: switching to colour frame buffer device 60x53
fb0: Virtual frame buffer device, using 1024K of video memory
Serial: STM32 USART driver
stm32serial.2: ttyS2 at MMIO 0x40004800 (irq = 39) is a STM32 USART Port
console [ttyS2] enabled
brd: module loaded
uclinux[mtd]: ROM probe address=0x8120000 size=0x59000
Creating 1 MTD partitions on "ROM":
0x000000000000-0x000000059000 : "ROMfs" 
ARMv7-M VFP Extension supported
VFS: Mounted root (romfs filesystem) readonly on device 31:0.
Freeing init memory: 16K
starting pid 27, tty '/dev/ttyS2': '/bin/login -f root'
Welcome to
          ____ _  _
         /  __| ||_|
    _   _| |  | | _ ____  _   _  _  _
   | | | | |  | || |  _ \| | | |\ \/ /
   | |_| | |__| || | | | | |_| |/    \
   |  ___\____|_||_|_| |_|\____|\_/\_/
   | |
   |_|

For further information check:
http://www.uclinux.org/

Jan  1 00:00:01 login[27]: root login on 'ttyS2'
~ #
```
