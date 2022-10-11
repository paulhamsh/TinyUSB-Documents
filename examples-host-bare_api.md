This program will read the raw USB input from a PIO host and output text to the UART.   


```
export BASE=pico
export PRJ=bare_api

mkdir $BASE
cd $BASE
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib
git clone https://github.com/raspberrypi/pico-sdk

cd pico-sdk/lib
git clone https://github.com/hathach/tinyusb  -b master
cd tinyusb
git submodule update --init --recursive
```

Then edit the file ```src/tusb_config.h```

```

// RHPort number used for host can be defined by board.mk, default to port 0
#ifndef BOARD_TUH_RHPORT
#define BOARD_TUH_RHPORT      1
#endif

#define CFG_TUH_MAX_SPEED     BOARD_TUH_MAX_SPEED

#if CFG_TUSB_MCU == OPT_MCU_RP2040
// Use pico-pio-usb as host controller for raspberry rp2040
#define CFG_TUH_RPI_PIO_USB   1
#endif

/* USB DMA on some MCUs can only access a specific SRAM region with restriction on alignment.
```

And edit file ```CMakeLists.txt```   

```
# For rp2040, un-comment to enable pico-pio-usb
family_add_pico_pio_usb(${PROJECT})

# due to warnings from other net source, we need to prevent error from some of the warnings options
target_compile_options(${PROJECT} PUBLIC
        -Wno-error=shadow
        -Wno-error=cast-align
        -Wno-error=cast-qual
        -Wno-error=redundant-decls
        -Wno-error=sign-conversion
        -Wno-error=conversion
        -Wno-error=sign-compare
        -Wno-error=unused-function

        -Wno-cast-qual
        -Wno-cast-align
        -Wno-sign-compare
        -Wno-shadow
        -Wno-redundant-decls
        -Wno-unused-function
        )


pico_enable_stdio_usb(${PROJECT} 0)
pico_enable_stdio_uart(${PROJECT} 1)
```

```family_add_pico_pio_usb``` is needed to include the PICO-PIO-USB files   
```pico_enable_stdio_usb(${PROJECT} 0)``` turns off USB console output   
```pico_enable_stdio_uart(${PROJECT} 1)``` turns on serial 0 console output (on GPIO 0)   

```
export PICO_SDK_PATH=~/$BASE/pico-sdk
cd ~/$BASE/pico-sdk/lib/tinyusb/examples/dual/$PRJ

mkdir build
cd build
cmake .. -DFAMILY=rp2040
make

cp $PRJ.uf2 /mnt/c/Users/Paul/Desktop
```

Then copy the .uf2 file to the Pico Pi.
