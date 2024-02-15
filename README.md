# ESPHome for Tuya SK20 WiFi Nebula Light

This repository contains an esphome configuration file for flashing esphome to Tuya SK20 nebula lights.
Thanks to the amazing work of a lot of other people no disassembly or soldering is required.
You can simply jailbreak the device using your laptop and flash ESPhome (or any other LibreTiny / WB3S-compatible firmware).

## References

The following repositories, projects and tools helped a lot in jailbreaking my nebula light:
- teardown, research and pictures: https://github.com/kireque/esphome_nebula_light
- basic/unfinished esphome config for included uC: https://github.com/fonix232/esphome_nebula_light
- exploit to flash tuya devices without disassembly: https://github.com/tuya-cloudcutter/tuya-cloudcutter
- esphome support for BK7231T devices: https://docs.libretiny.eu/
- tutorial on flashing esphome: https://docs.libretiny.eu/docs/flashing/tools/cloudcutter/
- discussion regarding the esphome configuration for this nebula device: https://github.com/kireque/esphome_nebula_light/issues/8

## Pictures

![](images/device-image.jpg)

![](images/esphome-screenshot.png)

## Basic instructions
This guide assumes you have a Linux computer and some basic terminal knowledge.
The used tools also have some prerequisites like `python`, `docker` and `plattform.io`

First create a `secrets.yaml` file inputting your own parameters (see an example in [secrets.yaml](secrets.yaml):
```yaml
wifi_ssid: "<your SSID>"
wifi_password: "<your wifi password>"
wifi_fallback_password: "<some random password>"
ota_password: "<some random password>"
api_password: "<either: your home assistant api password>"
```

Then modify the `example.yaml` file to suite your needs or use your **own** configuration and just import the `nebula_light_device.yaml` esphome package with a `device_name` variable defined:
```yaml
packages:
  platform: !include platform_bk72xx.yaml
  nebula: !include
    file: nebula_light_device.yaml
    vars:
      device_name: "Star Projector"
```

You can also import the esphome nebula package from a remote git repository by using:
```yaml
substitutions:
  device_name: "Star Projector"

packages:
  remote_package:
    url: https://github.com/M4GNV5/esphome-SK20-Nebula-Light.git
    ref: master
    files: [platform_bk72xx.yaml, nebula_light_device.yaml]
```

Then, build and flash the image using the following commands:
```bash
$ git clone https://github.com/M4GNV5/esphome-SK20-Nebula-Light
$ git clone https://github.com/tuya-cloudcutter/tuya-cloudcutter
$ cd esphome-SK20-Nebula-Light
$ pip install esphome
$ esphome compile example.yaml
$ cp ./.esphome/build/sternenhimmel/.pioenvs/sternenhimmel/image_bk7231t_app.ota.ug.bin ../tuya-cloudcutter/custom-firmware/
$ sudo ./tuya-cloudcutter.sh
```

The last program will guide you through the jailbreaking procedure, asking you for the following information. You will also have to long press the device button for a few seconds twice until it is slow blinking (WiFi AP mode):
- (2) Flash 3rd Party Firmware
- By manufacturer/device name
- Tuya Generic
- SK20 Smart Star Projector
- 1.1.2 - BK7231T / oem_bk7231t_light3_laser_nanxin
- `image_bk7231t_app.ota.ug.bin`

The nebula light should now connect to your Wifi and/or host its own fallback AP.
