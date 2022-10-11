## host_hid_to_device_cdc - using Pico Pi PIO USB

This example will run a USB device on the Pico Pi USB and a USB host on the PIOs (GPIO 2 and 3).

It uses GPIO 2 and 3 as D+ and D- on a USB hub.   
Also connect GND to GND (Pico Pi pin 38) and VBUS to VBUS (Pico Pi pin 40).   
USB uses +3.3v as logic 1 so this is ok for the Pico Pi.   

This program will accept a HID input (keyboard or mouse) and send the data as a CDC serial text output via the main USB socket.   

```
export BASE=pico2
export PRJ=host_hid_to_device_cdc

mkdir $BASE
cd $BASE
sudo apt install cmake gcc-arm-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib
git clone https://github.com/raspberrypi/pico-sdk

cd pico-sdk/lib
git clone https://github.com/hathach/tinyusb  -b master
cd tinyusb
git submodule update --init --recursive


export PICO_SDK_PATH=~/$BASE/pico-sdk
cd ~/$BASE/pico-sdk/lib/tinyusb/examples/dual/$PRJ

mkdir build
cd build
cmake .. -DFAMILY=rp2040
make

cp $PRJ.uf2 /mnt/c/Users/Paul/Desktop
```

Then copy the .uf2 file to the Pico Pi.
