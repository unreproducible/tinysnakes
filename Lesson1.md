# Tiny Snakes Lesson 1: Flash and blink

picture of esp8266 goes here


# Flashing your board

Follow [the flashing instructions on the micropython website](
https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/intro.html)
to flash Micropython on the board.

Note that we *are* using one of the boards that needs the `-fm dio` option.
Without it, it will look like it flashed fine, but the serial port will hang
and you won't get a nice prompt.  So your command line should look like this:

`esptool.py --port /dev/ttyUSB0 --baud 460800 write_flash --flash_size=detect
-fm dio 0 esp8266-20170108-v1.8.7.bin`

Only you'll need to change the port to be the right one for your device. (On
my Mac, for example, it's `/dev/tty.SLAB\_USBtoUART`)

FIXME: link to connection instructions here?

# Writing some python

Once you've connected successfully, you should see something like

```
ets_task(40100164, 3, 3fff829c, 4)
OSError: [Errno 2] ENOENT

MicroPython v1.9.1-8-g7213e78d on 2017-06-12; ESP module with ESP8266
Type "help()" for more information.
>>>
```

If you've used python before, that `>>>` prompt should look pretty familiar
because it's the same one you get if you just type `python` on the command
line.

You can now basically just type python.  For tradition's sake, try the old standard `print("hello world")`.

```python
>>> print("hello world!")
hello world!
>>>
```

## Blink the onboard LED

The equivalent to "hello world!" on hardware is usually "make an LED blink" so let's do that next.

The boards we're using have one built-in LED.  To talk to the LED, we're going to use a library called machine that you can import using the command `import machine`.  If you'd like to know more about what's available in the machine module, you can use the command `help(machine)` to get a list.

From there, we need to declare what pin we're talking to.  The onboard pin on the boards we're using today is on pin 2, so we initialize something at pin 2 using `led = machine.Pin(2, machine.Pin.OUT)`

```python
>>> import machine
>>> led = machine.Pin(2, machine.Pin.OUT)
```

Your LED should glow bright blue!
FIXME: picture?

Tired of being blinded by your LED?  You can also turn it on and off using `led.on()` and `led.off()` -- try it out!  You're going to notice something a little bit strange about this... What is it?

Now let's write a loop to make it blink.  First, let's import a time library so that we can slow down the blinking rate to human speed using `import time` then try a basic that turns it on and off with a wait in between

```python
>>> import time
>>> for i in range(10):
...     led.on()
...     time.sleep(0.5)
...     led.off()
...     time.sleep(0.5)
...
```

Hit backspace to get out of the indent and start running your loop.  Notice how the `>>>` prompt doesn't come back until it's done the loop.  You can abort it earlier using control-c


# Connect up an external LED

FIXME: pictures go here
