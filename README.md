# Sonix Flasher
A simple multi-platform utility for flashing QMK firmware into SONiX microcontrolers.

## Usage
### Entering bootloader

To perform firmware flashing, you need to boot your keyboard into bootloader mode. In order to do this, you can use one of the ways described below:

* If you are on stock firmware (and your keyboard is listed in the device list), click 'Reboot to Bootloader' button
* On flashed keyboard, you should be able to press `Fn + Esc` combination to immediately boot into bootloader
* As a last resort, you can open a keyboard and briefly short BOOT pin of the MCU with GND

**[NOTE]** If you have a jumploader, it’s strongly recommended to flash the jumploader on SN32F260 since the 260 series can become bricked if the bootloader is overrided. [See](https://github.com/SonixQMK/sonix-keyboard-bootloader#entering-the-bootloader)

### Flashing firmware

Put your keyboard in bootloader mode, set the correct qmk offset (which should be 0x200 only if you have jumploader flashed, so basically only on 260 series chips) and press `Flash QMK...` button

## Compiling
### Requirements
* Python >= 3.7
* libusb and libudev (only in Linux)

### Linux and Mac OS

```sh
python3 -m venv venv
source venv/bin/activate
pip install cython # only on Mac OS
pip install wheel
pip install -r requirements.txt
pyinstaller sonix-flasher.spec
```

To run an application you need to type in Linux:
`./dist/sonix-flasher/sonix-flasher`

And in Mac OS:
`open -n ./dist/sonix-flasher/sonix-flasher.app`

**[NOTE]** To run it for immediate use, just run `run.sh` and it'll set itself up and run. (only for Linux)

### NixOS

Alternatively, if you're running NixOS or have Nix installed, you can run:

```sh
nix shell
fbs run
```

### Windows

Open Powershell and type:

```powershell
python -m venv venv
. venv\Scripts\Activate.ps1
pip install -r requirements.txt
pyinstaller sonix-flasher.spec
```

## Running with sudo

It is highly recommended that instead of running Sonix Flasher with root permissions, you'll make yourself needed udev rules. 

Example of udev rule for Akko:
```sh
SUBSYSTEM=="usb", ATTR{idVendor}=="320f", ATTR{idProduct}=="5013", TAG+="uaccess", TAG+="udev-acl"
KERNEL=="hidraw*", ATTR{idVendor}=="320f", ATTR{idProduct}=="5013", TAG+="uaccess", TAG+="udev-acl"
```

Change `ATTR{idVendor}` and `ATTR{idProduct}` to values valid for your keyboard and type in terminal: `udevadm control --reload`

## Troubleshooting
### Keyboard isn't showing in device list
Firstly check if your keyboard is plugged in properly.

If you are on Windows, download [Zadig](https://zadig.akeo.ie/) to get VID and PID (first value in USB ID is VID and the second is PID). 

On Linux and Mac OS open terminal and type `lsusb`, then find your keyboard by name to get an USB ID (like in Zadig, first value is VID and the second is PID). 

[Create a new issue](https://github.com/SonixQMK/sonix-flasher/issues/new) with a name of your keyboard and its USB ID in description.

### Sonix Flasher is crashing with message like this:
```
Authorization required, but no authorization protocol specified

qt.qpa.xcb: could not connect to display :0
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "" even though it was found.
```

This means you are probably trying to run application with sudo on Wayland session. Wayland doesn't support running applications with sudo because of security reasons. You should refer to [Running with sudo](#running-with-sudo) section, or check [here](https://wiki.archlinux.org/title/Running_GUI_applications_as_root#Wayland) for a workaround.