# OPENOCD on RaspberryPi

本笔记记录了使用`openocd`在`树莓派`上烧录`stm32`芯片和`nrf52`芯片的操作过程

### 1. 编译openocd

因为种种原因，为了避免在树莓派上使用预编译的二进制所可能遭遇的兼容问题，我们需要在树莓派上自己编译一次`openocd`，过程比较简单

```shell
sudo apt update
sudo apt install git autoconf libtool make pkg-config libusb-1.0-0 libusb-1.0-0-dev
git clone https://github.com/ntfreak/openocd
cd openocd
./bootstrap
./configure --enable-sysfsgpio --enable-bcm2835gpio
make -j4
sudo make install
```

`install`完成之后，`sudo reboot now`重启一下，更新环境变量。在树莓派3上，`make`的过程可能需要十几分钟。

### 2. 配置参数

File:`raspi3.cfg`
```shell
#
# Config for using Raspberry Pi's expansion header
#
# This is best used with a fast enough buffer but also
# is suitable for direct connection if the target voltage
# matches RPi's 3.3V and the cable is short enough.
#
# Do not forget the GND connection, pin 6 of the expansion header.
#

interface bcm2835gpio

bcm2835gpio_peripheral_base 0x3F000000

# Transition delay calculation: SPEED_COEFF/khz - SPEED_OFFSET
# These depend on system clock, calibrated for stock 700MHz
# bcm2835gpio_speed SPEED_COEFF SPEED_OFFSET
bcm2835gpio_speed_coeffs 146203 36

# Each of the JTAG lines need a gpio number set: tck tms tdi tdo
# Header pin numbers: 23 22 19 21
#bcm2835gpio_jtag_nums 11 25 10 9

# Each of the SWD lines need a gpio number set: swclk swdio
# Header pin numbers: 23 22
bcm2835gpio_swd_nums 25 24

# If you define trst or srst, use appropriate reset_config
# Header pin numbers: TRST - 26, SRST - 18

# bcm2835gpio_trst_num 7
# reset_config trst_only

# bcm2835gpio_srst_num 24
# reset_config srst_only srst_push_pull

# or if you have both connected,
# reset_config trst_and_srst srst_push_pull
```

### 3. 编写烧录脚本

File:`f103_prog.cfg`
```shell
source ./raspi3.cfg
transport select swd
adapter_khz 4000
source [find target/stm32f1x.cfg]
program ./LED.hex verify reset exit
```


### 4. 连线烧录

![](port.jpg)

Program Stm32F103:`sudo openocd -f f103_prog.cfg`