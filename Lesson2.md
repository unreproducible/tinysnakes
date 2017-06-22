# Tiny Snakes Lesson 2: Wifi
## WIFI background ##
There is a single radio on the ESP8266, but it has some nice features:
* Client mode (able to connect to access points)
* AP mode (can provide an access point)
* Can do both "simultaneously"
* Raw frames are possible but complicated
* TCP/IP is mostly dealt with 
* MicroPython has an in-built web server that will provide a REPL prompt
  * We aren't going to play with this really here, there's too many devices here

## Dealing with AP mode ##

The ESP8266 with MicroPython will default to starting up the AP on, so lets deal with turning it off:

```python
>>> import network
>>> ap_if = network.WLAN(network.AP_IF)
```

ap_if meaning "access point interface" in this case.

To check what state the ap_if is in (I.E. is the AP mode on and broadcasting?)
  ```python
  ap_if.active()
  True
  ```

To find out what IP settings are currently set:
```python
>>> ap_if.ifconfig()
('192.168.4.1', '255.255.255.0', '192.168.4.1', '8.8.8.8')
```

Lets setup the client before turning off the AP

Noting that in the following example `sta_if` is the station, or client, interface.  You are basically connecting to a pre-existing wifi.  You could put anything into the SSID and Password below, but unless they match the network you won't get into anything

```python
>>> sta_if = network.WLAN(network.STA_IF)
>>> sta_if.active(True)
>>> sta_if.connect('<your ESSID>', '<your password>')
>>> # Loop till we actually get a successful connection
>>> while not sta_if.isconnected():
>>>     pass
...
...
...
>>> sta_if.ifconfig()
('192.168.0.2', '255.255.255.0', '192.168.0.1', '8.8.8.8')
>>> # Now disable AP mode, you don't need it any more
>>> ap_if.active(False)
```

## Sockets ##

TCP/IP and UDP sockets are availble, so lets go ahead and open one up and see where it gets us.

```python
>>> import socket
>>> addr_info = socket.getaddrinfo("towel.blinkenlights.nl", 23)
>>> addr = addr_info[0][-1]
>>> s = socket.socket()
>>> s.connect(addr)
```

You now have a connected socket to the remote server, we should likely pull some data

```python
>>> while True:
...     data = s.recv(500)
...     print(str(data, 'utf8'), end='')
...
```

When you are bored with this you can always < ctrl>+c out to end the loop

```python
>>> s.shutdown(1)
>>> s.close()
```

So lets move onto something a bit more complicated, like pulling a website down

```python
def http_get(url):
    _, _, host, path = url.split('/', 3)
    addr = socket.getaddrinfo(host, 80)[0][-1]
    s = socket.socket()
    s.connect(addr)
    s.send(bytes('GET /%s HTTP/1.0\r\nHost: %s\r\n\r\n' % (path, host), 'utf8'))
    while True:
        data = s.recv(100)
        if data:
            print(str(data, 'utf8'), end='')
        else:
            break
    s.close()
```

So lets actually snag the website now

```python
>>> http_get('http://micropython.org/ks/test.html')
```

## Going further ##
* Blink the LED on packet TX or RX
* Blink the LED based on status of events