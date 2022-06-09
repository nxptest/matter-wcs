# matter-wcs
<hr>

-   [Matter WCS](#matter-wcs)
-   [Introduction](#introduction)
-   [Building](#building)
-   [Flashing](#flashing)

<hr>

<a name="intro"></a>

## Introduction

This repository keeps Matter patches for different wireless devices of NXP.

The naming rule for the directory to store patches are as follows:

1. \<device\>/patches/\<git account\>/branch/\<branch name\>\<submodule\>
```
$ cd <device>
$ git clone https://github.com/<git account>/connectedhomeip
$ cd connectedhomeip
$ git checkout -b <branch name> origin/<brnach name>
$ git apply ../mw320/patches/<git account>/branch/<branch name>/*.patch
$ git submodule update --init
$ cd third_party/<submodule>/repo
$ git apply ../../../../mw320/patches/<git account>/branch/<branch name>/<submodule>/*.patch
$ cd ../../../
```
2. \<device\>/patches/\<git account\>/tag/\<tag name\>\<submodule\>
```
$ cd <device>
$ git clone https://github.com/<git account>/connectedhomeip
$ cd connectedhomeip
$ git checkout tags/<tag_name> -b <tag name>
$ git apply ../mw320/patches/<git account>/tag/<tag name>/*.patch
$ cd third_party/<submodule>/repo
$ git apply ../../../../mw320/patches/<git account>/tag/<tag name>/<submodule>/*.patch
$ cd ../../../
```

<a name="building"></a>

## Building

\<MW320\>

Used directory "patches/nxptest/branch/TE9_upstream" as an example.

Building the example application is quite straightforward. It can be done via
following commands:
```
$ git clone https://github.com/nxptest/matter-wcs
$ cd matter-wcs/mw320
$ git clone https://github.com/nxptest/connectedhomeip
$ cd connectedhomeip
$ git checkout -b TE9_upstream origin/TE9_upstream
$ git apply ../mw320/patches/nxptest/branch/TE9_upstream/*.patch
$ git submodule update --init
$ cd third_party/mbedtls/repo
$ git apply ../../../../mw320/patches/nxptest/branch/TE9_upstream/mbedtls/*.patch
$ cd ../../..
$ source scripts/activate.sh
$ cd examples/all-clusters-app/nxp/mw320
$ gn gen out/debug
$ ninja -v -C out/debug
```
Example application binary file "all-cluster-mw320.bin" will be generated under
directory "out/debug".

Note:
1. "git submodule update --init" only needs to be issued for the first time in order
   to download MW320 SDK for Matter.
2. "source scripts/activate.sh" can be omitted if your
   environment is already setup without issues.

<a name="flashdebug"></a>

## Flashing

\<MW320\>

Connect MW320 to Ubuntu USB port and open Linux text-based serial port communications
program at second USB interface (/dev/ttyUSB1):
```
$ TERM=linux minicom -D /dev/ttyUSB1 -b 115200
```

Prepare MW320 download firmware image:
```
$ ln -sf third_party/connectedhomeip/third_party/nxp/mw320_sdk/repo mw320_sdk
$ mw320_sdk/tools/mw_img_conv/bin/mw_img_conv mcufw out/debug/all-cluster-mw320.bin out/debug/all-cluster-mw320.mcufw.bin 0x1F010000
$ cp out/debug/all-cluster-mw320.mcufw.bin mw320_sdk/mw320_matter_flash/Matter/.
```

Install OpenOCD (Open On-Chip Debugger):
```
$ sudo apt-get install openocd
```

Flashing firmware image to MW320:
```
$ cd mw320_sdk/mw320_matter_flash
$ sudo python2 flashprog.py -l Matter/layout-4m.txt --boot2 Matter/boot2.bin --wififw Matter/mw32x_uapsta_W14.88.36.p172.bin --mcufw Matter/all-cluster-mw320.mcufw.bin -r
```

After MW320 is reset, console will allow you to enter commands.
