# (Prerequisites):

[Raspberry Pi Pico](https://www.raspberrypi.com/products/raspberry-pi-pico/)
<br>
[Micro usb data cable](https://www.amazon.com/Amazon-Basics-Charging-Transfer-Gold-Plated/dp/B0711PVX6Z/ref=sr_1_6?crid=BR5WJVTPUJB0&dib=eyJ2IjoiMSJ9.N8rC158T4mQZ4YhbxwqSh4UC0TslWwmEmp8BLj3UDRc0uQEOvc8a2Xe6jvMwoI6iiZjXpNStX5UZild8CiFuY8RDRAL69q4M7qCSxOB3OV0zVvrSZid016f8ONqv7FHktuAZMwFCKQn9vQiDXz871xewhB-qNca8JS8SE1XvzuBYghlNRGh3CeoCweUId5uXzqC33L__SwRfsMfv8mr-KacQPj8ckPizN4Ek5JYVWXBJcoYs6XMoYpP9DvBgnLrhoveA0AZpBy3rLn3PPM0zNAsiubK1LzRgmr0vOzjmSe4.6eFb0MdBUkzY7MXoHkuV4C0ZyXkzVm_SVd44YZ97Uhk&dib_tag=se&keywords=micro+usb+data+cable&qid=1721480930&sprefix=micro+usb+data+%2Caps%2C101&sr=8-6)
<br>
[BMP280 thermistor](https://www.adafruit.com/product/2651)
<br>
Solder, soldering iron, small effort of soldering skills <urls>

Wire thermistor to pico:
<br>
-power to power

-ground to ground

-SCK to GPIO1

-SDI to GPIO2

[insert pics]

# (Mounting of thermistor)

<insert pictures and recomended locations>

# (Flashing Pico)

plug micro usb cable into pico - do not plug into printer (yet)

hold down bootsel on pico

while holding bootsec, insert usb cable into printer

type in `lsusb` to confirm the pico displays as a rp2040 boot device

ssh into printer
```
cd ~/klipper

make clean

make menuconfig
```

[insert pic of menu flash options for rp2040]

push `Q` to quit and save 

type in `make flash FLASH_DEVICE=2e8a:0003`

printer will now flash pico and reboot the pico (wait 30 sec)

after pico reboot enter `ls /dev/serial/by-id/*` to find the pico

(insert pic)


# (printer.cfg configuration)

open printer.cfg

Copy/paste information below into printer.cfg:
```
[mcu pico]
serial: /dev/serial/by-id/<your pico device id>

[temperature_sensor chamber]
sensor_type: BME280
min_temp: 20
max_temp: 65
i2c_address: 119
#   Default is 118 (0x76). The BMP180, BMP388 and some BME280 sensors
#   have an address of 119 (0x77).
i2c_mcu: pico
#   The name of the micro-controller that the chip is connected to.
#   The default is "mcu".
i2c_bus: i2c0a
#   If the micro-controller supports multiple I2C busses then one may
#   specify the micro-controller bus name here. The default depends on
#   the type of micro-controller.
#i2c_software_scl_pin:
#i2c_software_sda_pin:
#   Specify these parameters to use micro-controller software based
#   I2C "bit-banging" support. The two parameters should the two pins
#   on the micro-controller to use for the scl and sda wires. The
#   default is to use hardware based I2C support as specified by the
#   i2c_bus parameter.
#i2c_speed:
#   The I2C speed (in Hz) to use when communicating with the device.
#   The Klipper implementation on most micro-controllers is hard-coded
#   to 100000 and changing this value has no effect. The default is
#   100000. Linux, RP2040 and ATmega support 400000.
gcode_id:
```
now open the Macros.cfg file and copy/paste the following:

```
######################################################################
# BMP280/BME280/BME680 Environmental Sensor
######################################################################

# The macro below assumes you have a BME280 sensor_type defined in one
# of the applicable sections in printer.cfg, such as:
#
#[temperature_sensor my_sensor]
#sensor_type: BME280
#gcode_id: AMB
#
# Note the format of the parameter SENSOR in the macro below.  The BME280
# sensor status can be accessed using the format "bme280 <section_name>".
# The example section above is named "my_sensor", thus the bme280 can be
# queried as follows:
#
# QUERY_BME280 SENSOR='bme280 my_sensor'
#
# Since a default parameter is defined one could simply enter QUERY_BME280
# as well.

[gcode_macro QUERY_BME280]
gcode:
    {% set sensor = printer["bme280 my_sensor"] %}
    {action_respond_info(
        "Temperature: %.2f C\n"
        "Pressure: %.2f hPa\n"
        "Humidity: %.2f%%" % (
            sensor.temperature,
            sensor.pressure,
            sensor.humidity))}
```

after inserting into printer config, save and restart firmware

chamber temp now displays on main dashboard
