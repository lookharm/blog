+++
title = 'Compile Kernel Module'
date = 2026-03-29T23:05:44+07:00
draft = false
+++

# Compile Kernel Module

* Kernel กับ โมดูลควรใช้ config เดียวกัน 
* มีสองส่วนที่ต้องเขียน Makefile และ Kbuild
* Makefile มี target ชื่อ build,kbuild คำสั่งจะไปเรียกใช้ Makefile ของ Linux Kernel อีกที
* Linux Kernel Makefile จะมี option ให้เลือกคอมไพล์จากโมดูลจากด้านนอก ถ้าลองดูในโค้ดของ Makefile

โค้ดบางส่วนจาก Linux Kernel Makefile
```Makefile
1686 # External module support.
1687 # When building external modules the kernel used as basis is considered
1688 # read-only, and no consistency checks are made and the make
1689 # system is not used on the basis kernel. If updates are required
1690 # in the basis kernel ordinary make commands (without M=...) must
1691 # be used.
1692 #
1693 # The following are the only valid targets when building external
1694 # modules.
1695 # make M=dir clean     Delete all automatically generated files
1696 # make M=dir modules   Make all modules in specified dir
1697 # make M=dir           Same as 'make M=dir modules'
1698 # make M=dir modules_install
1699 #                      Install the modules built in the module directory
1700 #                      Assumes install directory is already created
```

* M=dir โดยที่ dir คือโค้ดโมดูลที่ต้องการคอมไพล์

ตัวอย่างที่ 1

Makefile
```Makefile
KDIR = /lib/modules/`uname -r`/build

kbuild:
        make -C $(KDIR) M=`pwd`

clean:
        make -C $(KDIR) M=`pwd` clean
```

Kbuild
```Makefile
EXTRA_CFLAGS = -Wall -g

obj-m        = modul.o
```

ตัวอย่างที่ 2

Makefile
```Makefile
build: $(KCONFIG) skels/Kbuild 
        $(MAKE) -C $(KDIR) M=$(KDIR)/tools/labs/skels ARCH=$(ARCH) modules 
```
* dependencies:
  * KCONFIG kernel config file e.g /linux/.config
  * Kbuild: Kbuild file

Kbuid
```Makefile
ccflags-y += -Wno-unused-function -Wno-unused-label -Wno-unused-variable 
obj-m += ./kernel_modules/9-dyndbg/
obj-m += ./kernel_modules/6-cmd-mod/
obj-m += ./kernel_modules/8-kdb/
obj-m += ./kernel_modules/5-oops-mod/
obj-m += ./kernel_modules/3-error-mod/
obj-m += ./kernel_modules/4-multi-mod/
obj-m += ./kernel_modules/7-list-proc/
obj-m += ./kernel_modules/1-2-test-mod/
```

## Link
* https://linux-kernel-labs.github.io/refs/heads/master/labs/kernel_modules.html
