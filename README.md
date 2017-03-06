# BeagleBone Blue
Howto use [BeagleBone Blue](https://github.com/jadonk/beaglebone-blue) with [ArduPilot](https://github.com/ArduPilot/ardupilot)

# Hardware

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

## Run ArduPilot
