Prerequisits:
Pico [url]
Micro usb data cable [url]
BMP280 thermistor [url]
Solder, soldering iron, small effort of soldering skills <urls>

Wire thermistor to pico
-power to power
-ground to ground
-SCK to GPIO1
-SDI to GPIO2
[insert pics]

(Mounting of thermistor)

[insert pictures and recomended locations]

(Flashing Pico)

plug micro usb cable into pico - do not plug into printer (yet)
hold down bootsel on pico
while holding bootsec, insert usb cable into printer
perform lsusb, find pr2040 boot device

ssh into printer
cd ~/klipper
make clean
make menuconfig
[insert pic of menu flash options for rp2040]
push `Q` to quit and save 
type in `make flash FLASH_DEVICE=2e8a:0003`
printer will now flash pico and reboot the pico (wait 30 sec)
after pico reboot enter `ls /dev/serial/by-id/*` to find the pico
(insert pic)


(printer.cfg configuration)

open printer.cfg
insert:

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

after inserting into printer config, save and restart firmware

chamber temp now displays on main dashboard
