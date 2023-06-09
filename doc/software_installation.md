[Back to README.md](../README.md)

# Table of Contents

- [Software Installation](#software-installation)
  - [macOS or Linux](#macOS-or-Linux)
  - [Raspberry Pi OS](#Raspberry-Pi-OS)
    - [common](#common)
    - [GPS module](#gps-module)
    - [ANT+ USB dongle](#ant+-usb-dongle)
    - [Display](#display)
    - [I2C sensors](#i2c-sensors)
- [Quick Start](#quick-start)
  - [Run on X Window](#run-on-x-window)
  - [Run on console](#run-on-console)
    - [Manual execution](#manual-execution)
    - [Run as a service](#run-as-a-service)
- [Usage](#usage)
  - [Button](#button)
    - [Software button](#software-button)
    - [Hardware button](#hardware-button)
  - [Menu screen](#menu-screen)
  - [Settings](#settings)
    - [setting.conf](#settingconf)
    - [setting.pickle](#settingpickle)
    - [layout.yaml](#layoutyaml)
    - [map.yaml](#mapyaml)
    - [config.py](#configpy)
  - [Prepare course files and maps](#prepare-course-files-and-maps)

# Software Installation

Assume Python version 3 environment. Version 2 is not supported.

## macOS or Linux

```
$ git clone https://github.com/hishizuka/pizero_bikecomputer.git
$ pip3 install PyQt5 numpy oyaml pillow polyline aiohttp aiofiles qasync garminconnect 
$ pip3 install git+https://github.com/hishizuka/pyqtgraph.git
$ pip3 install git+https://github.com/hishizuka/crdp.git
$ cd pizero_bikecomputer
```

Note:
Pyqt version 5.15.0 in macOS has [a qpushbutton issue](https://bugreports.qt.io/browse/QTBUG-84852), so installing newest version(5.15.1~) is recommended.

## Raspberry Pi OS

Raspberry Pi OS (32-bit) with desktop is recommended.

The program works with Raspberry Pi OS (32-bit) Lite, but missing libraries will need to be installed. Especially installing python3-pyqt5 with `apt` command will also installs massive libraries of desktop software, so building PyQt5 package is recommended.

Here is [my setup guide in Japanese](https://qiita.com/hishi/items/8bdfd9d72fa8fe2e7573).

### Common

Install in the home directory of default user "pi". Also, your Raspberry Pi is connected to internet and updated with `apt-get update & apt-get upgrade`.


```
$ cd
$ git clone https://github.com/hishizuka/pizero_bikecomputer.git
$ sudo apt-get install python3-pip cython3 cmake gawk python3-numpy python3-pyqt5 sqlite3 libsqlite3-dev libatlas-base-dev python3-aiohttp python3-aiofiles
$ sudo pip3 install oyaml sip polyline garminconnect stravacookies qasync
$ sudo apt-get install wiringpi python3-smbus python3-rpi.gpio python3-psutil python3-pil
$ sudo pip3 install git+https://github.com/hishizuka/pyqtgraph.git
$ sudo pip3 install git+https://github.com/hishizuka/crdp.git
$ cd pizero_bikecomputer
```

### GPS module

#### UART GPS

Assume Serial interface is on and login shell is off in raspi-config and GPS device is connected as /dev/ttyS0. If GPS device is /dev/ttyAMA0, modify gpsd config file(/etc/default/gpsd).

```
$ sudo apt-get install gpsd gpsd-clients python3-dateutil
$ sudo pip3 install gps3 timezonefinder 
$ sudo cp install/etc/default/gpsd /etc/default/gpsd
$ sudo systemctl enable gpsd
```

Check with `cgps` or `gpsmon` command.

#### I2C GPS

Assume I2C interface is on in raspi-config.

```
$ sudo apt-get install python3-dateutil
$ sudo pip3 install timezonefinder pa1010d
```

Check with [pa1010d example program](https://github.com/pimoroni/pa1010d-python/blob/master/examples/latlon.py)


### ANT+ USB dongle

```
$ sudo apt-get install libusb-1.0-0 python3-usb
$ sudo pip3 install git+https://github.com/hishizuka/openant.git
```
 

### Display

Assume SPI interface is on in raspi-config.

#### MIP Reflective color LCD module and Adafruit SHARP Memory Display Breakout

You can use python3-pyqt5 package. Don't need building Qt.

```
$ sudo apt-get install python3-pigpio
$ sudo systemctl enable pigpiod
$ sudo systemctl start pigpiod
```

#### PiTFT 2.4

see [hardware_installation_pitft.md](./hardware_installation_pitft.md#display)

#### E-ink Displays

You can use python3-pyqt5 package too.

##### PaPiRus ePaper / eInk Screen HAT for Raspberry Pi

Follow [official setup guide](https://github.com/PiSupply/PaPiRus)

##### DFRobot e-ink Display Module for Raspberry Pi 4B/3B+/Zero W

Follow [official setup guide](https://wiki.dfrobot.com/Raspberry_Pi_e-ink_Display_Module_SKU%3A_DFR0591) and install manually.


### I2C sensors

Assume I2C interface is on in raspi-config.

#### Main sensors (pressure, temperature, IMU and light)

Install pip packages of the sensors you own.

Here is an example.
```
$ sudo pip3 install adafruit-circuitpython-bmp280
```

| Manufacturer | Sensor | additional pip package |
|:-|:-|:-|
| [Pimoroni](https://shop.pimoroni.com) | [Enviro pHAT](https://shop.pimoroni.com/products/enviro-phat) | None |
| [Adafruit](https://www.adafruit.com) | [BMP280](https://www.adafruit.com/product/2651) | None |
| [Adafruit](https://www.adafruit.com) | [BMP390](https://www.adafruit.com/product/4816) | None |
| [Sparkfun](https://www.sparkfun.com/) | [BMP581](https://www.sparkfun.com/products/20170) | None |
| [Adafruit](https://www.adafruit.com) | [LPS33HW](https://www.adafruit.com/product/4414) | adafruit-circuitpython-lps35hw |
| [Strawberry Linux](https://strawberry-linux.com) | [LPS33HW](https://strawberry-linux.com/catalog/items?code=12133) | None |
| [DFRobot](https://www.dfrobot.com) | [BMX160+BMP388](https://www.dfrobot.com/product-1928.html) | BMX160(*1) | 
| [Adafruit](https://www.adafruit.com) | [LSM6DS33 + LIS3MDL](https://www.adafruit.com/product/4485) | adafruit-circuitpython-lsm6ds adafruit-circuitpython-lis3mdl |
| [Sparkfun](https://www.sparkfun.com/) | [ISM330DHCX + MMC5983MA ](https://www.sparkfun.com/products/19895) | adafruit-circuitpython-lsm6ds |
| [Adafruit](https://www.adafruit.com) | [LSM9DS1](https://www.adafruit.com/product/4634) | adafruit-circuitpython-lsm9ds1 | 
| [Adafruit](https://www.adafruit.com) | [BNO055](https://www.adafruit.com/product/4646) | adafruit-circuitpython-bno055(*2) | 
| [Adafruit](https://www.adafruit.com) | [VCNL4040](https://www.adafruit.com/product/4161) | adafruit-circuitpython-vcnl4040 |
| [ozzmaker](https://ozzmaker.com) | [Berry GPS IMU v4](https://ozzmaker.com/product/berrygps-imu/) | adafruit-circuitpython-lsm6ds adafruit-circuitpython-lis3mdl |
| [GPS PIE](https://gps-pie.com/) | [GPS PIE](https://gps-pie.com/) | adafruit-circuitpython-bno055(*2) |
| [waveshare](https://www.waveshare.com/) | [Environment Sensor HAT](https://www.waveshare.com/environment-sensor-hat.htm) | adafruit-circuitpython-bme280 adafruit-circuitpython-icm20x adafruit-circuitpython-tsl2591 adafruit-circuitpython-ltr390 adafruit-circuitpython-sgp40 |

*1 Install manually https://github.com/spacecraft-design-lab-2019/CircuitPython_BMX160

*2 You must enable i2c slowdown. Follow [the adafruit guide](https://learn.adafruit.com/circuitpython-on-raspberrypi-linux/i2c-clock-stretching).


If you want to get a more accurate direction with the geomagnetic sensor, install a package that corrects the geomagnetic declination.

```
$ sudo pip3 install magnetic-field-calculator
```

#### Button SHIM

```
$ sudo apt-get install python3-buttonshim
```

#### PiJuice HAT

Follow [official setup guide](https://github.com/PiSupply/PiJuice/tree/master/Software) of PiSupply/PiJuice


# Quick Start

If cython is available, it will take a few minutes to run for the first time to compile the program.

## Run on X Window

If you use Raspberry Pi OS with desktop, starting on X Window (or using VNC) at first would be better.

```
$ python3 pizero_bikecomputer.py
```

### PiTFT

see [hardware_installation_pitft.md](./hardware_installation_pitft.md#run-on-x-window)

## Run on console

### Manual execution

#### MIP Reflective color LCD module, SHARP Memory Display or E-Ink displays

Before run the program, add the following environment variable.

```
$ QT_QPA_PLATFORM=offscreen python3 pizero_bikecomputer.py
```

#### PiTFT

see [hardware_installation_pitft.md](./hardware_installation_pitft.md#run-on-console)


### Run as a service

If you use displays in console environment not X Window, install auto-run service and shutdown service.

#### auto-run setting

If you use MIP Reflective color LCD module, SHARP Memory Display or E-Ink displays, modify install/etc/systemd/system/pizero_bikecomputer.service.

```
ExecStart=/home/pi/pizero_bikecomputer/exec-mip.sh
```

Install service scripts.

```
$ sudo cp install/etc/systemd/system/pizero_bikecomputer.service /etc/systemd/system/
$ sudo cp install/usr/local/bin/pizero_bikecomputer_shutdown /usr/local/bin/
$ sudo cp install/etc/systemd/system/pizero_bikecomputer_shutdown.service /etc/systemd/system/
$ sudo systemctl daemon-reload
$ sudo systemctl enable pizero_bikecomputer.service
$ sudo systemctl enable pizero_bikecomputer_shutdown.service
```

#### start the service

The output of the log file will be in "/home/pi/pizero_bikecomputer/log/debug.txt".

```
$ sudo systemctl start pizero_bikecomputer.service
```


# Usage

## Button

### Software button

<img width="400" alt="screen01" src="https://user-images.githubusercontent.com/12926652/206077256-f8bda5e5-e4a3-4c39-a7ff-ea343067756c.png">

The buttons at the bottom of the screen are assigned the following functions from left to right. 

| Button | Short press | Long press |
|:-|:-|:-|
| Left (<) | Screen switching(Back) | None |
| LAP | Lap | Reset |
| MENU | Menu | None |
| Start/Stop  | Start/Stop | Quit the program |
| Right (>) | Screen switching(Forward) | None |

### Hardware button

The hardware buttons are designed to roughly match the software screen.
You can change both short and long presses in "modules/config.py".

#### PiTFT 2.4

see [hardware_installation_pitft.md](./hardware_installation_pitft.md#hardware-button)

### Button shim

<img src="https://user-images.githubusercontent.com/12926652/91799330-cfc50580-ec61-11ea-9045-e1991aed205c.png" width=240 />

#### Main

From left to right, the button assignments are as follows.

| Button | Short press | Long press |
|:-|:-|:-|
| A | Left (<) | None |
| B | Lap | Reset |
| C | Screenshot | None |
| D | Start/Stop | None |
| E | Right (>) | Menu |

#### Map

| Button | Short press | Long press |
|:-|:-|:-|
| A | Left (<) | None |
| B | Zoom out | Reset |
| C | Change button mode(*1) | None |
| D | Zoom in | None |
| E | Right (>) | Menu |

Another button mode

| Button | Short press | Long press |
|:-|:-|:-|
| A | Move left | None |
| B | Move down | Zoom out |
| C | Restore button mode | Change move amount |
| D | Move up | Zoom in |
| E | Move right | Search route(*) |

(*)Search route by Google Directions API. Set your API key in setting.conf.

#### Course Profile

| Button | Short press | Long press |
|:-|:-|:-|
| A | Move left | None |
| B | Zoom out | None |
| C | Restore button mode | None |
| D | Zoom in | None |
| E | Move right | None |

Another button mode

| Button | Short press | Long press |
|:-|:-|:-|
| A | Move left | None |
| B | Zoom out | None |
| C | Restore button mode | None |
| D | Zoom in | None |
| E | Move right | None |

#### Menu

In the menu, the button assignments are changed.

| Button | Short press | Long press |
|:-|:-|:-|
| A | Back | None |
| B | Brightness control(*) | None |
| C | Enter | None |
| D | Select items (Back) | None |
| E | Select items (Forward) | None |

(*) If you use the MIP Reflective color LCD with backlight model.

### Garmin Edge Remote (ANT+)

#### Main

The button assignments are as follows.

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | Left (<) | Right (>) |
| CUSTOM | Change button mode | Menu |
| LAP | Lap | None |

Another button mode

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | ANT+ Light ON/OFF | Brightness control |
| CUSTOM | Restore button mode | None |
| LAP | Start/Stop | None |

#### Map

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | Left (<-) | Right (->) |
| CUSTOM | Change button mode | Zoom out |
| LAP | Zoom in | None |

Another button mode

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | None | None  |
| CUSTOM | Restore button mode| Zoom out |
| LAP | Zoom in | None |

#### Course Profile

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | Left (<-) | Right (->) |
| CUSTOM | Change button mode | Zoom out |
| LAP | Zoom in | None |

Another button mode

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | None | None  |
| CUSTOM | Restore button mode| Move left |
| LAP | Move right | None |

#### Menu

In the menu, the button assignments are changed.

| Button | Short press | Long press |
|:-|:-|:-|
| PAGE | Select items (Forward) | None |
| CUSTOM | Select items (Back) | None |
| LAP | Enter | None |


## Menu screen

<img width="400" alt="menu-01-all" src="https://user-images.githubusercontent.com/12926652/206076181-5118d71a-1750-4570-8bd0-923b4b9699eb.png">

### Sensors

<img width="400" alt="menu-02-sensors" src="https://user-images.githubusercontent.com/12926652/206076191-4b8a4084-64a0-443b-a434-f6c6b4d51e2a.png">

- ANT+ Sensors
  - Pairing with ANT+ sensors. 
  - You need to install the ANT+ library and to set [ANT section](#ant-section) of setting.conf with `status = True`.
  - The pairing setting is saved in setting.conf when a sensor is connected, so it will be automatically connected next time you start the program.
- ANT+ MultiScan
- Wheel Size
  - Enter the wheel circumference in mm when the ANT+ speed sensor is available.
  - It is used to calculate the distance.
  - The default value is 2,105mm, which is the circumference of 700x25c tire.
  - The value is saved in setting.conf
- Adjust Altitude
  - Enter the current altitude to correct the sea level and increase the accuracy when an I2C pressure sensor is connected.

### Courses

<img width="400" alt="menu-03-courses" src="https://user-images.githubusercontent.com/12926652/206076194-a0b3b356-fbda-4591-ba27-02701e461aaa.png">

- Local Storage
  - Select course .tcx file in `courses` folder.
- Ride sith GPS
  - If you set token in setting.conf, select course from Ride with GPS. Internet access is required. Sample image are shown as belows.
- Google Directions API mode
  - If you set token in [GOOGLE_DIRECTION_API section](#google_direction_api-section) of setting.conf, select route type: Car or Bicycle.

Load course with the right icon (>).

<img width="400" alt="RidewithGPS-01" src="https://user-images.githubusercontent.com/12926652/206076210-9c50f789-bac3-4bd0-8209-9dea3a61a132.png"> <img width="400" alt="RidewithGPS-02" src="https://user-images.githubusercontent.com/12926652/206076212-8696ac34-c9e6-485f-b1ba-687c0d2a0061.png">

### Upload Activity

Uploads the most recent activity record file(.fit) created by the reset operation after the power is turned on.

<img width="400" alt="menu-04-upload_activity" src="https://user-images.githubusercontent.com/12926652/206076198-55803175-ef4c-4f9b-9408-b44dbe98b1b3.png">

- Strava
  - You need to set the Strava Token in [Strava API section](#strava_api-section) of setting.conf).
- Garmin
  - You need to set the Garmin setting in [GARMINCONNECT_API section](#garminconnect_api-section) of setting.conf.
- Ride with GPS
  - You need to set the Ride with GPS Token in [RIDEWITHGPS_API section](#ridewithgps_api-section) of setting.conf.

### Map

<img width="400" alt="menu-05-map" src="https://user-images.githubusercontent.com/12926652/206076200-383c1d24-ec26-4b79-95e9-a6fd88e161dd.png"> <img width="400" alt="menu-06-map_overlay" src="https://user-images.githubusercontent.com/12926652/206076202-29989a71-34c0-4892-8433-48305868326d.png">

- Select Map
  - Select a standard map.
- Map Overlay
  - Toggle map overlay(heatmap, rain map and wind map) and select an overlay map.
  - Heatmaps are cached, but rain map and wind map require internet connection to fetch the latest images.

#### Heatmap

Strava heatmap (bluered)

![map_overlay-strava](https://user-images.githubusercontent.com/12926652/205793586-0b754cde-d1e7-4e57-81d2-2bbd60fc8b11.png)

#### Rain map

RainViewer

![map_overlay_rainviewer](https://user-images.githubusercontent.com/12926652/205876664-ae1b629c-5b3f-4d8a-b789-d3ac24753d7f.png)

国土地理院(Japan)

<img src ="https://user-images.githubusercontent.com/12926652/205563333-549cf4dc-abbd-4392-9233-b8391687e0bc.png" width=400/> 

#### Wind map

openportguide

![map_overlay_weather openportguide de](https://user-images.githubusercontent.com/12926652/205876684-253b672f-615d-410c-8496-5eb9a13b2558.png)

### Profile

If ANT+ powermeter is available, set both parameters are used in W'balance (%). They are determined by histrical activity data with bycicle power with [GoldenCheetah](http://www.goldencheetah.org) or [intervals.icu](https://intervals.icu).

<img width="400" alt="menu-07-profile" src="https://user-images.githubusercontent.com/12926652/206076204-197f2454-940b-4267-a626-52740391ddac.png">

### System

<img width="400" alt="menu-08-system" src="https://user-images.githubusercontent.com/12926652/206076205-a4fa9665-bc2a-4339-8c8b-15cde6a19004.png">

- Network
  - See below.
- Update
  - Update the program.
  - It just does a `git pull origin master` in update.sh.
- Power Off
  - Power off the raspberry pi zero when the program is started with the service.
- Debug log
  - View "log/debug.log".

<img width="400" alt="menu-09-network" src="https://user-images.githubusercontent.com/12926652/206076207-9a284166-ce16-4589-8ef3-b428ee09e7a5.png">

- BT Tethering
  - If `/usr/local/bin/bt-pan` is available, start bluetooth tethering with devices of [BT_ADDRESS_section](#bt_address-section) in setting.conf.
  - Latest Raspberry Pi OS has only python version 3, so bt-pan must be modified to work with python version 3(not shown).
- Phone Msg
  - This is experimental. Assume wifi or bluetooth network(Use tethering when outdoors).
  - Start http server and receive messages via GET method.
  - The http address is http://address:8080/message?app={app}&title={title}&message={message}
  - The IP address can be get from "IP Address".
  - Phone notifications can be forwarded by Android [MacroDroid](https://play.google.com/store/apps/details?id=com.arlosoft.macrodroid) or iOS Shortcuts.
  - <img width="400" alt="message" src="https://user-images.githubusercontent.com/12926652/206078983-fcf6b101-21c5-4c12-a118-d25a1e133d6a.png">

## Settings

There are five different configuration files. You need to edit at the first "setting.conf" and don't need to care about the other files.

### setting.conf

The settings are dependent on the user environment.
GENERAL -> display must be set.

#### GENERAL section

- `display`
  - Set the type of display.
  - There are definitions in `modules/config.py` for the resolution and availability of the touchscreen.
  - `None`: default (no hardware control)
  - `PiTFT`: PiTFT2.4 (or a PiTFT2.8 with the same resolution)
  - `MIP`: MIP color reflective LCD module 2.7 inch.
  - `MIP_Sharp`: SHARP Memory Display Breakout
  - `Papirus`: PaPiRus ePaper / eInk Screen HAT
  - `DFRobot_RPi_Display`: e-ink Display Module
- `autostop_cutoff`
  - Set the threshold for the speed at which the stopwatch will automatically stop/start after it is activated.
  - The default value is `4` [km/h].
- `wheel_circumference`
  - Set the wheel circumference required for ANT+ speed sensor use.
  - It can also be set on the screen.
  - The default value is `2105` (unit is mm) for 700x25c.
- `gross_ave_speed`
  - Set the gross average speed, which is used in the brevet and the like.
  - It is used for cycling long distances with a set time limit.
  - The screen shows the actual gross average and the gained time from this gross average speed.
  - The default value is `15` [km/h].
- `lang`
  - The language setting of the label of items.
  - The default is `EN`.
  - You can set other languages with `G_LANG` in modules/gui_config.py. Samples of `JA` are available.
- `font_file`
  - Set the the full path of the font which you want to use.
  - Place the fonts in `fonts/` folder.
- `map`
  - Set the map.
  - The `G_MAP_CONFIG` in modules/config.py provides some preset definitions.
  - `toner`: A map for monochrome colors. [http://maps.stamen.com/toner/](http://maps.stamen.com/toner/)
  - `wikimedia`: An example map of a full-color map. [https://maps.wikimedia.org/](https://maps.wikimedia.org/)
  - `jpn_kokudo_chiri_in`: A map from Japan GSI. [https://cyberjapandata.gsi.go.jp](https://cyberjapandata.gsi.go.jp)
  - You can add a map URL to map.yaml. Specify the URL in tile format (tile coordinates by [x, y] and zoom level by [z]). And The map name is set to this setting `map`.
  - Also, you can set raster mbtiles mapsets generated from [mb-util](https://github.com/mapbox/mbutil). It is sqlite3 db packed with raster maptile images. The definition in map.yaml is following with sample mbtile map named `sample_mbtile` placed at `maptile/sample_mbtile.mbtiles`.

```
map.yaml entry

  sample_mbtile:
    url: 
    attribution: some attribution.
    use_mbtiles: true
```

#### ANT+ section

- Enable ANT+ with `status = True`.
- Additional setting is not necessary because the settings are written when pairing ANT+ sensors.
- If there are some settings, the program will connect at startup.

#### Power section

If ANT+ power meter is available, set `cp` as CP and `w_prime` as W prime balance.

#### SENSOR_IMU section
In modules/sensor_i2c.py, use the change_axis method to change the axis direction of the IMU (accelerometer/magnetometer/gyroscope) according to its mounting direction.
The settings are common, so if you use individual sensors, make sure they are all pointing in the same direction.

X, Y, and Z printed on the board are set to the following orientations by default.

- X: Forward. Positive value for upward rotation with accelerometer.
- Y: Left. Positive value for upward rotation with accelerometer.
- Z: Downward. Positive value at rest with accelerometer.

Axis conversion is performed with the following variables.

- `axis_swap_xy_status`: Swaps the X and Y axes.
  - The default is `False`, or `True` if you want to apply it.
- `axis_conversion_status`: Inverts the signs of X, Y and Z.
  - Change to `False` by default, or `True` if you want to apply it.
  - `axis_conversion_coef`: Fill in [X, Y, Z] with ±1.

`mag_axis_swap_xy_status`, `mag_axis_conversion_status` and `mag_axis_conversion_coef` can be set if magnetometer axes are different from accelerometer and gyroscope. For example, [SparkFun 9DoF IMU Breakout - ISM330DHCX, MMC5983MA (Qwiic)](https://www.sparkfun.com/products/19895).

`mag_declination` is automatically set by magnetic-field-calculator package.

#### STRAVA_API section

Set up for uploading your .fit file to Strava in the "Strava Upload" of the menu. The upload is limited to the most recently reset and exported .fit file.

To get the Strava token, see "[Trying the Authorization Method (OAuth2) of the Strava V3 API (In Japanese)](https://hhhhhskw.hatenablog.com/entry/2018/11/06/014206)".
Set the `client_id`, `client_secret`, `code`, `access_token` and `refresh_token` as described in the article. Once set, they will be updated automatically.

#### STRAVA_COOKIE section

If you want to use Strava HeatMap, set `email` and `password`.

#### RIDEWITHGPS_API section

If you want to use heatmap or upload activities to RidewithGPS, set your `token` of the Ride with GPS API.
Apikey is assumed as `pizero_bikecomputer`.

#### GARMINCONNECT_API section

If you want to upload activities to Garmin Connect, set your `email` and `password`.

#### GOOGLE_DIRECTION_API section

If you want to search for a route on a map, set your `token` of the Google Directions API.

#### OPENWEATHERMAP_API section

If you want to correct the altitude using a barometric pressure sensor, set your `token` of the OpenWeatherMap API. 

#### BT_ADDRESS section

If you want to use bluetooth tethering, set address.
Assume bluetooth pairing has already been set up(not shown).

```
device_A = 01:23:45:67:89:AB
device_B = CD:EF:01:23:45:67
```

### setting.pickle

It stores temporary variables such as values for quick recovery in the event of a power failure and sensor calibration results.

Most of them are deleted on reset.

### layout.yaml

Set up the placement of each item on the display of a screen consisting only of numerical values.
(Maps and graphs cannot be edited with this setting.)

The following is an example of a top screen.

```
MAIN:
  STATUS: true
  LAYOUT:
    Power: [0, 0, 1, 2]
    HR: [0, 2]
    Speed: [1, 0]
    Cad.: [1, 1]
    Timer: [1, 2]
    Dist.: [2, 0]
    Work: [2, 1]
    Ascent: [2, 2]
```

- `MAIN`: The name is optional. It is not used in the program, but the following `STATUS` and `LAYOUT` are displayed on one screen. The number of screens can be increased or decreased.
- `STATUS`: Show this screen or not.
  - Set the boolean value of `true` or `false` of yaml format.
- `LAYOUT`: Specify the position of each element.
  - Each element is defined in modules/gui_congig.py under `G_ITEM_DEF`. You can also add your own variables to the modules/gui_congig.py file.
  - The position is set up in the form of [Y, X], with the top left as the origin [0, 0], the right as the positive direction of the X axis, and the bottom as the positive direction of the Y axis. The implementation is the coordinate system of QGridLayout.
  - If you want to merge multiple cells, the third argument should be the bottom Y coordinate + 1, and the fourth argument should be the right X coordinate + 1. For example, `Power: [0, 0, 1, 2]` merges the [0, 0] cell with the right next [0, 1] cell.

### map.yaml

Register the map name, tile URL and copyright in this file.
An example of Strava HeatMap is shown below.

```
strava_heatmap_hot:
  url: https://heatmap-external-b.strava.com/tiles-auth/ride/hot/{z}/{x}/{y}.png?px=256
  attribution: strava
```

- Line 1: Map name
  - This is the string to be set to [GENERAL](#general-section) -> map in setting.conf.
- Second line: tile URL
  - Set the tile URL. Tile coordinates X, Y, and zoom Z should be listed with `{x}`, `{y}`, and `{z}`.
- Line 3: Copyright.
  - Set the copyright required for the map.

### config.py

There are some settings which the user doesn't need to care about and some variables defined in the above configuration file.

## Prepare course files and maps

Put course.tcx file in course folder. The file name is fixed for now. If the file exists, it will be loaded when it starts up.

To download the map in advance, run the program manually with the --demo option. It will start in demo mode.

```console
$ python3 pizero_bikecomputer.py --demo
```

Press the left button to move to the map screen and leave it for a while. The current position will move along the course and download the required area of the map. 


[Back to README.md](/README.md)
