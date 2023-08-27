# P-Cube Klipper config

This is my backup place for my currently used klipper config file.

It is base on [MainsailOS](https://docs-os.mainsail.xyz/) and includes all of my custom files.

* [Klipper Config Ref](https://www.klipper3d.org/Config_Reference.html)
* [Klipper TMC](https://www.klipper3d.org/TMC_Drivers.html)
* [Klipper GCodes](https://www.klipper3d.org/G-Codes.html)
* [Moonraker (API) Config](https://moonraker.readthedocs.io/en/latest/configuration/)
* [KlipperScreen (Touchscreen) Config](https://klipperscreen.readthedocs.io/en/latest/Configuration/)
* [CrowsNest (CamProxy) Config](https://crowsnest.mainsail.xyz/configuration/sample-config)
* [Spoolman](https://github.com/Donkie/Spoolman)
* [Klipper Adaptive Meshing & Purging](https://github.com/kyleisah/Klipper-Adaptive-Meshing-Purging)

## Install peripherals

### Enable direct serial via 3-wire

Issue with instructions: https://github.com/Klipper3d/klipper/issues/2913

#### tl;dr

##### Adjust `/boot/config.txt`

The following lines worked for me:

```text
enable_uart=1
dtoverlay=disable-bt
dtoverlay=pi3-miniuart-bt
```

##### Adjust `/boot/cmdline.txt`

Make sure no console is specified for UART debugging.

##### Adjust `printer.cfg`

```cfg
[mcu]
serial: /dev/ttyAMA0
restart_method: command
```

### Touchscreen Rotation

* Comment out line `dtoverlay=vc4-kms-v3d` ➡ `#dtoverlay=vc4-kms-v3d`
* Add line `display_rotate=2` to rotate for 180°

### Touchscreen Driver

⚠ __DON'T ever use the install scripts of them__ ⚠ (They will overwrite all existing config and do strange stuff)

https://joy-it.net/de/products/RB-LCD-5

Setup instructions: https://www.waveshare.com/wiki/5inch_HDMI_LCD

#### tl;dr

##### Edit `/boot/config.txt`

Add the following lines

```text
hdmi_group=2
hdmi_mode=87
hdmi_cvt 800 480 60 6 0 0 0
hdmi_drive=1
dtoverlay=ads7846,cs=1,penirq=25,penirq_pull=2,speed=50000,keep_vref_on=0,swapxy=0,pmax=255,xohms=150,xmin=200,xmax=3900,ymin=200,ymax=3900
```

##### Do touch calibration

```bash
# Install packages
sudo apt-get install xserver-xorg-input-evdev xinput-calibrator -y

# Copy configs to later time executed
sudo cp -rf /usr/share/X11/xorg.conf.d/10-evdev.conf /usr/share/X11/xorg.conf.d/45-evdev.conf          

# Add touch panel options
sudo tee /usr/share/X11/xorg.conf.d/99-calibration.conf > /dev/null <<EOT
Section "InputClass"
        Identifier "calibration"
        MatchProduct "ADS7846 Touchscreen"
        Option "Calibration" "157 3965 164 3902"
        Option "TransformationMatrix" "-1 0 1 0 -1 1 0 0 1" # This turns also the touch 180°
        Option "SwapAxes" "0"
        Option "EmulateThirdButton" "1"
        Option "EmulateThirdButtonTimeout" "1000"
        Option "EmulateThirdButtonMoveThreshold" "300"
EndSection
EOT
```

##### Do a calibration

* Calibrate with `DISPLAY=:0 xinput_calibrator`
* Adjust `/usr/share/X11/xorg.conf.d/99-calibration.conf` with the new values of `Calibration` if the differ a lot


## Customization

### KlipperScreen

To customize the KlipperScreen UI switch the files below with the red dice png

* main.svg
* klipper.svg
