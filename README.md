# BeagleBone Blue
Howto use [BeagleBone Blue](https://github.com/jadonk/beaglebone-blue) with [ArduPilot](https://github.com/ArduPilot/ardupilot)

[![alt text](https://img.youtube.com/vi/v2xWtlyYrtE/0.jpg)](https://youtu.be/v2xWtlyYrtE)

[![alt text](https://img.youtube.com/vi/8bVc9X869pw/0.jpg)](https://youtu.be/8bVc9X869pw)

# Hardware

![alt text](https://github.com/jadonk/beaglebone-blue/blob/master/docs/BeagleBone_Blue_balloons.png "BeagleBone Blue")

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
5. Add BLUE DTB: `sudo sed -i 's/#dtb=$/dtb=am335x-boneblue-ArduPilot.dtb/' /boot/uEnv.txt`
6. Set clock to fixed 1GHz: `sudo sed -i 's/GOVERNOR="ondemand"/GOVERNOR="performance"/g' /etc/init.d/cpufrequtils`
7. Update scripts: `cd /opt/scripts && sudo git pull`
8. Install RT Kernel: `sudo /opt/scripts/tools/update_kernel.sh --ti-rt-channel --lts-4_4`
9. Reboot system: `sudo reboot`

## Compile ArduPilot
1. Clone ArduPilot code: `git clone https://github.com/ArduPilot/ardupilot.git`
2. `cd ardupilot`
3. `git checkout master`
4. `git submodule update --init --recursive`
5. `./waf configure --board=blue`
6. `./waf (take about 1h20m)`
7. `cp build/blue/bin/* /home/debian/`

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

# License

<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">BeagleBone Blue ArduPilot</span> by <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/mirkix" property="cc:attributionName" rel="cc:attributionURL">Mirko Denecke</a> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

UNLESS OTHERWISE MUTUALLY AGREED TO BY THE PARTIES IN WRITING, LICENSOR OFFERS THE WORK AS-IS AND MAKES NO REPRESENTATIONS OR WARRANTIES OF ANY KIND CONCERNING THE WORK, EXPRESS, IMPLIED, STATUTORY OR OTHERWISE, INCLUDING, WITHOUT LIMITATION, WARRANTIES OF TITLE, MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE, NONINFRINGEMENT, OR THE ABSENCE OF LATENT OR OTHER DEFECTS, ACCURACY, OR THE PRESENCE OF ABSENCE OF ERRORS, WHETHER OR NOT DISCOVERABLE. SOME JURISDICTIONS DO NOT ALLOW THE EXCLUSION OF IMPLIED WARRANTIES, SO SUCH EXCLUSION MAY NOT APPLY TO YOU. EXCEPT TO THE EXTENT REQUIRED BY APPLICABLE LAW, IN NO EVENT WILL LICENSOR BE LIABLE TO YOU ON ANY LEGAL THEORY FOR ANY SPECIAL, INCIDENTAL, CONSEQUENTIAL, PUNITIVE OR EXEMPLARY DAMAGES ARISING OUT OF THIS LICENSE OR THE USE OF THE WORK, EVEN IF LICENSOR HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES.
