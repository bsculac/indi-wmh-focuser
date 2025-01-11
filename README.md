# indi-wmh-focuser
INDI focus driver for Waveshare Stepper Motor HAT stepper motor controller board. This allows controlling a stepper motor directly from a Raspberry Pi, with no external controller necessary. Additionally, the HAT can power the Raspberry Pi, so only a single 12V power connection will be needed, instead of a 12V and a 5V connection.

Now with Rock Pi 4 and ODROID-N2/N2+ support! More SBC's with compatible 40-pin GPIO headers will be supported in time.

# Building
This assumes you already have INDI installed.

First, let's install some build tools, if you haven't already.
```
sudo apt install build-essential cmake libgpiod-dev
```

If you built INDI from source, you can skip this step. Otherwise, you must install the INDI development libraries.
```
sudo apt install libindi-dev
```

Okay, we're ready to get the source and build it.
```
git clone https://github.com/k-ross/indi-wmh-focuser.git
cd indi-wmh-focuser
mkdir build
cd build
```

### Raspberry Pi
If you're building for the Raspberry Pi, run
```
cmake -DCMAKE_INSTALL_PREFIX=/usr ..
make
sudo make install
```
### Rock Pi 4 running Debian
```
cmake -DCMAKE_INSTALL_PREFIX=/usr -DROCKPI=1 ..
make
sudo make install
```
### Rock Pi 4 running Armbian
```
cmake -DCMAKE_INSTALL_PREFIX=/usr -DROCKPI_ARMBIAN=1 ..
make
sudo make install
```
### ODROID-N2 and ODROID-N2+ running Armbian
```
cmake -DCMAKE_INSTALL_PREFIX=/usr -DODROID_N2=1 ..
make
sudo make install
```
### Raspberry Pi Running AstroArch
If you're building on [AstroArch](https://github.com/devDucks/astroarch)/Arch Linux, you will need to install libgpiod verson 1.6.x from source. The following commands will build and install v1.6.5 with C++ bindings.
```
cd ~
git clone https://git.kernel.org/pub/scm/libs/libgpiod/libgpiod.git
cd libgpiod
git checkout v1.6.5
./autogen.sh --enable-tools=yes --enable-bindings-cxx --prefix=/usr
make
sudo make install
```
You may also need to install g++ and cmake using pacman, see https://wiki.archlinux.org/title/C and https://wiki.archlinux.org/title/Pacman. Once you have libgpiod installed, cd to the build directory crated above and build this driver using the commands for Raspberry Pi. If you get errors involving `std::filesystem` during compilation, try changing `set(CMAKE_CXX_STANDARD 14)` to `set(CMAKE_CXX_STANDARD 17)` in indi-wmh-focuser/CMakeLists.txt.
## Running KStars remotely
If you're going to run KStars on a computer other than your Raspberry Pi, then you need to install an XML file to the computer with KStars. Open KStars, go to Settings, then Configure KStars, then go down to INDI. There will be an entry titled "INDI Drivers XML Directory". That directory is where you will want to copy the indi_wmh_focuser.xml file. On my Windows machine, that location is "C:/Users/Kevin/AppData/Local/indi"

# Running
Start your INDI server with Waveshare Motor HAT driver:

`indiserver <your normal indiserver arguments> indi_wmh_focuser`

Start KStars with Ekos, edit your profile (or create a new one), and select "Waveshare Motor HAT Focuser" in the Focuser dropdown. If you don't see Waveshare Motor HAT Focuser as an option, then you didn't copy the XML file to the right place, or you need to restart KStars after copying the XML file.

Now you can enjoy autofocus!

# Tuning
On the options tab in the INDI control panel, is an option titled "Delay per step". Stepper motors will lose torque when you run them faster. This will allow you to slow it down, to get more torque. The default of 0 will run at full speed, but without a lot of torque. On my setup, I find 200 to work well. 
