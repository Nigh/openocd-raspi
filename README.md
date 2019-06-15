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

### 3. 编写烧录脚本

### 4. 连线烧录

![](port.jpg)