# My Personal ZMK Keyboard Configs

This repository contains my personal ZMK configuration files for the eyelash_sofle keyboard together with a dongle

The repository is initialized from [a741725193/zmk-sofle-dongle](https://github.com/a741725193/zmk-sofle-dongle)

## Build (Locally)

Use container with podman

Reference: [official tutorial](https://v0-3-branch.zmk.dev/docs/development/local-toolchain/setup/container?container=podman)

### Clone Repository

Below repositories should be cloned

- zmk: <https://github.com/zmkfirmware/zmk.git>
- this repo: <https://github.com/Jacky-Lzx/keyboard.zmk-config>
- zmk-dongle-display: <https://github.com/englmaxi/zmk-dongle-display>

File structure:

- `/Users/zexili/Documents/Github/keyboard.zmk`
- `/Users/zexili/Documents/Github/keyboard.zmk-config`
- `/Users/zexili/Documents/Github/keyboard.zmk-modules/zmk-dongle-display`

### Initialize Container

Directory: the `zmk` folder

```fish
podman build -t zmk-v0.3 -f Dockerfile ./.devcontainer
```

It seems that `-t` does not set the container name well. The name of the container should be manually modified after the
running of it.

### Run Container

```fish
set -g ZMK_HOME /Users/zexili/Documents/Github/keyboard.zmk
set -g ZMK_CONFIG /Users/zexili/Documents/Github/keyboard.zmk-config
set -g ZMK_MODULES /Users/zexili/Documents/Github/keyboard.zmk-modules
podman run -it \
    --security-opt label=disable \
    --workdir /workspaces/zmk \
    -v $ZMK_HOME:/workspaces/zmk \
    -v $ZMK_CONFIG:/workspaces/zmk-config \
    -v $ZMK_MODULES:/workspaces/zmk-modules \
    -p 3000:3000 \
    zmk-v0.3 /bin/bash
```

### Restart

Based on the document, the container should be restarted

### Compile

```fish
west build \
    -d build/eyelash_sofle_dongle \
    -b nice_nano_v2 \
    -- \
    -DSHIELD="eyelash_sofle_central_dongle dongle_display" \
    -DZMK_CONFIG="/workspaces/zmk-config/config" \
    -DZMK_EXTRA_MODULES="/workspaces/zmk-modules/zmk-dongle-display;/workspaces/zmk-config"

west build \
    -d build/eyelash_sofle_left \
    -b nice_nano_v2 \
    -- \
    -DSHIELD="eyelash_sofle_peripheral_left nice_view" \
    -DZMK_CONFIG="/workspaces/zmk-config/config" \
    -DZMK_EXTRA_MODULES="/workspaces/zmk-modules/zmk-dongle-display;/workspaces/zmk-config"

west build \
    -d build/eyelash_sofle_right \
    -b nice_nano_v2 \
    -- \
    -DSHIELD="eyelash_sofle_peripheral_right nice_view" \
    -DZMK_CONFIG="/workspaces/zmk-config/config" \
    -DZMK_EXTRA_MODULES="/workspaces/zmk-modules/zmk-dongle-display;/workspaces/zmk-config"
```

> [!TIP] Build times can be significantly reduced after the initial build by omitting all build arguments except the
> build directory, e.g. `west build -d build/left`. The additional options and intermediate build outputs from your
> initial build are cached and reused for unchanged files.

## 键盘说明文件中的部分内容

- 键盘侧面有一个拨动开关，左右两侧各有一个。拨动到下侧为打开状态，充电时需要打开开关。充电时间一般6-8个小时，取决于电池容量。充电时，绿色指示灯常亮。充电完毕，绿色指示灯熄灭。断开开关时接入USB电源，绿灯闪烁。
- 键盘开关附近有一个按压开关，按下为键盘重启。插上USB线，0.5秒内连续按两次键盘进入刷固件模式。刷固件模式时蓝灯呼吸状态。刷固件模式未连接USB，蓝灯快速闪烁。键盘连接USB并进入刷固件状态后，键盘会变成一个U盘盘符，显示在我的电脑里。此时将左右手固件，分别放入两个键盘虚拟出的U盘盘符里即完成固件更新。左键盘和右键盘刷固件时不用区分先后顺序。
- 键盘开机后左右键盘和dongle会自动连接，dongle是主设备。配对过程在第一次更新固件时即完成。后续固件更新并不会清除配对信息。所以分别更新固件后左右手键盘还会和dongle保持连接。如果需要重置连接状态，可以分别刷入reset固件，清除配对信息。清除配对信息后，刷入自己需要更新的键盘固件后，键盘和dongle会自动连接。

- 电池的拨动开关寿命要比reset开关低，所以外出携带时可以使用soft off 深度睡眠功能代替关闭电池供电。此时键盘任何按键都不会触发，键盘处于深度睡眠状态。进入方式为同时按下Q S Z 三个按键两秒钟。退出soft off状态的唯一方法是按一下reset开关。(**存疑**)

### 硬件介绍

- sofle无线系列键盘搭载nrf52840 MCU
- 搭载2000mah锂电池(505060)
- 电池接口为PH2.0插口，用户如果外购电池，需要注意插头正负极。
- 绿色指示灯是充电指示功能，常亮是充电中，闪烁是电池未连接或者电源开关没有打开，熄灭表示充电完成。正常使用不需要频繁关闭电源开关，只有键盘预计半年以上不会使用时才需要关闭电源，避免锂电池损坏。锂电池有保护板，充满后会自动停止充电。键盘可以使用手机充电头和手机充电线进行充电。
- 蓝色指示灯表示主控芯片工作状态。呼吸状态表示芯片处于BootLoader状态且USB已经连接电脑。快速闪烁表示芯片处于BootLoader状态，但未能和电脑建立连接。主控芯片正常启动后运行ZMK固件时应为熄灭状态。
- 电源开关型号：MKS12C02
- Reset按压开关型号：TS24CA
