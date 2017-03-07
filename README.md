# BeagleBone Blue
Howto use [BeagleBone Blue](https://github.com/jadonk/beaglebone-blue) with [ArduPilot](https://github.com/ArduPilot/ardupilot)

# Hardware

## Receiver
The RC receiver signal has to be connected to connector E4 pin 4. Make sure the signal does not exceed 3.3 volts, otherwise your BeagleBone Blue can be damaged.

## GPS
I recommend to use a u-blox M8N GPS. Connect the GPS to the GPS connector. Make sure the signal does not exceed 3.3 volts, otherwise your BeagleBone Blue can be damaged.

# Software

## Prepare BeagleBone Blue
1. Update software: `sudo apt update && sudo apt upgrade -y`
2. Install software: `sudo apt install -y bb-cape-overlays cpufrequtils g++ liblttng-ust-dev pkg-config gawk git make screen python python-dev python-lxml python-pip`
3. Install Python library: `sudo pip install future`
4. Set link to pkg-config: `sudo ln -s pkg-config /usr/bin/arm-linux-gnueabihf-pkg-config`
5. Set clock to fixed 1GHz: `sudo sed -i 's/GOVERNOR="ondemand"/GOVERNOR="performance"/g' /etc/init.d/cpufrequtils`
5. Update scripts: `cd /opt/scripts && sudo git pull`
6. Install RT Kernel: `sudo /opt/scripts/tools/update_kernel.sh --ti-rt-channel --lts-4_4`
7. Reboot system: `sudo reboot`
8. Get DTB sources: `git clone https://github.com/RobertCNelson/dtb-rebuilder.git`
9. Change dir: `cd dtb-rebuilder`
9. Checkout 4.4-ti branch: `git checkout 4.4-ti`
10. Patch file: `sed -i 's/0x03c 0x36/0x03c 0x35/' src/arm/am335x-boneblue.dts`
11. Compile DTB: `make`
12. Install DTB: `sudo make install`
13. Reboot system: `sudo reboot`

## Compile ArduPilot
1. Clone ArduPilot code: `git clone https://github.com/ArduPilot/ardupilot.git`
2. `cd ardupilot`
3. `git checkout master`
4. `git submodule update --init --recursive`
5. `./waf configure --board=blue`
6. `./waf (take about 1h20m)`
7. `cp build/bbbmini/bin/* /home/debian/`

## Run ArduPilot


### Automatic start ArduPilot after boot

If ArduPilot should start automatically at boot time follow the instructions below:

Edit `/etc/rc.local` with `sudo nano /etc/rc.local`
Modify file to (use your ArduPilot file and parameter):
```
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

/bin/sleep 10
/bin/echo uart > /sys/devices/platform/ocp/ocp\:P9_21_pinmux/state
/bin/echo uart > /sys/devices/platform/ocp/ocp\:P9_22_pinmux/state
/home/debian/arducopter -B /dev/ttyO2 -C /dev/ttyUSB0 > /home/debian/arducopter.log &

exit 0
```

1. Save file: `Strg + o + Enter`
2. Exit nano: `Strg + x`
3. Reboot BegaleBone with `sudo reboot`
