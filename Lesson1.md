# Tiny Snakes Lesson 1: Flash and blink

picture of esp8266 goes here


# Flashing your board

Follow [the flashing instructions on the MicroPython website](
https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/intro.html)
to flash MicroPython on the board.

Note that we *are* using one of the boards that needs the `-fm dio` option.
Without it, it will look like it flashed fine, but the serial port will hang
and you won't get a nice prompt.  So your command line should look like this:

`esptool.py --port /dev/ttyUSB0 --baud 460800 write_flash --flash_size=detect
-fm dio 0 esp8266-20170108-v1.8.7.bin`

Only you'll need to change the port to be the right one for your device. (On
my Mac, for example, it's `/dev/tty.SLAB\_USBtoUART`)

Next, [follow the instructions to get a REPL prompt](https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/repl.html).

# Writing some Python

Once you've connected successfully, you should see something like

```
MicroPython v1.9.1-8-g7213e78d on 2017-06-12; ESP module with ESP8266
Type "help()" for more information.
>>>
```
(There may be some other messages above that one.)

If you've used Python before, that `>>>` prompt should look pretty familiar because it's the same one you get if you just type `python` on the command line.  (The deep dark secret of MicroPython is that it's basically Python, just a smaller version of it, so if you know Python, you pretty much already know MicroPython!)

For tradition's sake, try the old standard `print("hello world")`.

```python
>>> print("hello world!")
hello world!
>>>
```

The fun thing about MicroPython compared to other embedded development is that you can do it all interactively over the serial port.  It makes it very easy to start playing!

## Blink the onboard LED

The equivalent to "hello world!" on hardware is usually "make an LED blink" so let's do that next.

The boards we're using have one built-in LED which you may have seen flash when you first connected the board.  To talk to the LED, we're going to use a library called `machine` that you can import using the command `import machine`.  If you'd like to know more about what's available in the machine module, you can use the command `help(machine)` to get a list.

From there, we need to declare what pin we're talking to.  The onboard pin on the boards we're using today is on pin 2, so we initialize something at pin 2 using `led = machine.Pin(2, machine.Pin.OUT)`

```python
>>> import machine
>>> led = machine.Pin(2, machine.Pin.OUT)
```

Your LED will glow bright blue!

FIXME: picture?

You can turn the LED on and off using `led.on()` and `led.off()` -- try it out!  Try *both* commands and see what happens.  You're going to notice something a little bit strange about this... What is it?

Now let's write a loop to make it blink.  First, let's import a time library so that we can slow down the blinking rate to human speed using `import time` then try a basic loop that turns it on and off with a wait in between

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

I'm guessing you aren't going to want to type this out every time you want to flash the LED, so let's try turning this into a function you can call:

```python
>>> def flashled(repeats, sleeptime):
...     for i in range(repeats):
...         led.on()
...         time.sleep(sleeptime)
...         led.off()
...         time.sleep(sleeptime)
...
...
>>>
```

Now try running your new function as `flashled(3, .5)`.  What do you need to do to make your board flash out the Morse code for SOS?

# Connect up an external LED

FIXME: pictures go here

Next up, we want to connect an external LED.  It's going to work pretty much like using the internal one, only it won't be on pin 2 this time.  Take a look at the [schematic for the board](https://acrobotic.com/featured/acr-00018) to learn what the pins are.

To connect up the external LED, you need to connect both the input and the ground wires.  We could wire it up all directly, but we're going to make it a little bit easier for ourselves by plugging the ESP8266 into a breadboard.  Pull out the little white breadboard in your package and plug the ESP8266 into the center so that there's a pin hole sticking out on either side of the inputs as follows:

FIXME: picture

From there, we're going to use jumper cables to wire up the LED.  Refresher: the LED has two legs on it, the long one is the positive one which we'll connect to a GPIO on the board.  The short leg is the negative one and we'll need to connect it to ground.  

Take a look at the board: you'll see that there's 4 pins marked GND.  Those are our ground pins.  You can use any of them, but we'll use the one closest to our GPIO.  Take a look at the [schematic for the board](https://acrobotic.com/featured/acr-00018) (scroll down if you can't see it on your screen) and you'll see that there are a bunch of different GPIOs you can choose from, and some of them are available to do more than one thing.  We're going to use GPIO5 which on the board is labelled D1.  

Choose any two colours of wires and make sure you have the long leg side of the LED plugged in beside the pin marked D1, and the short leg side of the LED plugged in beside one of the pins labelled GND as follows:

FIXME: picture

Note that convention would have the GND wire be black, but as long as you remember which is which it doesn't matter.

To make it turn on, we're going to need to set up a new led object that talks to GPIO5 as follows, then turn it on:

```python
>>> ledext = machine.Pin(5, machine.Pin.OUT)
>>> ledext.on()
```

Note how this time `on()` and `off()` behave as expected!  That built-in LED on pin 2 was wired backwards, but the other GPIOs behave the way they are supposed to.

# Saving programs


Running in interactive mode is fun for the purpose of a tutorial, but what if you want to save your program and have it run every time the board gets plugged in?  Don't worry, MicroPython sets up a file system on any device with more than 1Mbyte of storage, and you can write and store programs there.  You can [read more about the filesystem here](https://docs.micropython.org/en/latest/esp8266/esp8266/tutorial/filesystem.html). There are two special files on a MicroPython system: `boot.py` runs first and then the `main.py` script is executed (assuming the file exists, in both cases).  You can write whatever you want to these files.

FIXME: more instructions here y/n?