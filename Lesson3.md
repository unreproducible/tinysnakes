# Tiny Snakes Lesson 3: Working with a sensor

## I2C / SPI / GPIO / UART / Etc

There's a lot of different ways to connect things back to the MCU, they all have advantages and disadvantages.  We aren't going to get into this too much, and We'd encourage you to go and look into these different techs and where they may or may not be useful.

## BMP280 ##

The BMP280 is a barrometric pressure sensor, in a family of environmental sensors from Bosch.  Information, including datasheets can be found here:
* General information: <https://www.bosch-sensortec.com/bst/products/all_products/bmp280>
* Datasheet: <https://www.bosch-sensortec.com/bst/products/all_products/bmp280>

## Something intresting about MiroPython + ESP8266 ##

When you go to hook up things to most MCUs you tend to want to take advantage of the hardware blocks that are present, and there's usually good reason for this.  In most cases an MCU doesn't have a lot of underlying horse power and dealing with the signalling for certain protocols isn't doable in software.

MicroPython on the ESP8266 however decided to implement I2C completely in software, which has a unique advantage: we can use just about any pins for I2C communication.  If you haven't done hardware before, this is a huge boon, and will make our lives today a little easier in setting up the BMP280

## What you need to wire up ##
Pins you'll need to wire up:
* 3v3 to 3v3
* GND to GND
* SCL to a GPIO, note which one
* SDA to a GPIO, note which one

I used D5 and D6, but you can try others.  Noting of course some of the other pins may be in use and things may break.  This is hardware, go poke around and try things, you LIKELY won't let the magic smoke out.

NOTE: Remember to wire things up when it's NOT plugged into power.

## Installing Ampy ##

We are going to need to upload the driver for the BMP280, so go and install Ampy from Adafruit

You can follow their instructions here: <https://learn.adafruit.com/micropython-basics-load-files-and-run-code/install-ampy>

## Snag the BME280 driver ##

The BME280 and BMP280 use the same driver, and you can snag it by running:

```bash
# git clone https://github.com/catdog2/mpy_bme280_esp8266.git
```

## Upload The driver ##

This is fairly straight forward thankfully

```bash
# cd mpy_bme280_esp8266
# ampy -p /dev/ttyUSB0 -b 115200 put bme280.py
#
```

NOTE: Make sure you do not have a console terminal open when you run ampy, things will not work correctly if you have.  Also if your port is different, you should make that change approriately.

## After uploading ##

You are ready to try accessing things, open up the serial terminal and get the REPL prompt back, then:

```python
>>> import machine
>>> import bme280
>>> i2c = machine.I2C(scl=machine.Pin(<Pin Number>), sda=machine.Pin(<Pin Number>))
>>> bme = bme280.BME280(i2c=i2c)
>>> print(bme.values)
('24.6C', '1015.06hPa', '0.00%')
>>>
```

Your program should run and you are now accessing the sensor and could do things with it.

## Going Further ##
* Make a website that tells you the temperature and pressure
* Graph it on a website
* Figure out trends over time
* Upload the data to mqtt