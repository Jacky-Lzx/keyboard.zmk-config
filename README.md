# My Personal ZMK Keyboard Configs

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
west build -d build/eyelash_sofle_dongle -b nice_nano_v2 -S studio-rpc-usb-uart -- -DSHIELD="eyelash_sofle_central_dongle dongle_display" -DZMK_CONFIG="/workspaces/zmk-config" -DZMK_EXTRA_MODULES="/workspaces/zmk-modules/zmk-dongle-display" -DCONFIG_ZMK_STUDIO=y -DCONFIG_ZMK_STUDIO_LOCKING=n
west build -d build/eyelash_sofle_left -b nice_nano_v2 -- -DSHIELD="eyelash_sofle_peripheral_left nice_view" -DZMK_CONFIG="/workspaces/zmk-config"
west build -d build/eyelash_sofle_right -b nice_nano_v2 -- -DSHIELD="eyelash_sofle_peripheral_right nice_view" -DZMK_CONFIG="/workspaces/zmk-config"
```

> [!TIP] Build times can be significantly reduced after the initial build by omitting all build arguments except the
> build directory, e.g. `west build -d build/left`. The additional options and intermediate build outputs from your
> initial build are cached and reused for unchanged files.
