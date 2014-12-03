---
layout:     post
title:      Communication & Application Programming
image:      img/application_header.jpg
number:     '11 & 12'
---

### Bluetooth

I started work on my final project, a sous vide immersion cooker. I want to be able to interact with the device with my phone or tablet, so I decided to use Bluetooth. I originally used the Bluetooth 3.0 boards that are in the shop, but I found that it's impossible to interact with Bluetooth 3.0 from iOS. Instead, I purchased a BLE Mini device from [RedBearLab](http://redbearlab.com/blemini/). The board acts like a typical serial device, requiring TX, RX, VCC and GND connections. I initially wired the device to an old board to test out the Bluetooth, and later built a prototype of the board for my final project.

While you can access the normal Bluetooth serial from the Arduino console, Bluetooth BLE requires a special connection. I wanted to test with the phone, so I used [BLE Controller app](http://redbearlab.com/app/ios) from RedBearLab which provides a simple serial interface on the iPhone.

### Board
Since my Arduino application was larger than 4kb, I was forced to use a larger microcontroller than the ATTiny45. Unfortunately, the lab does not stock ATTiny85s, so I used the ATMega328 instead. The solder pads on this guy are much smaller:

{% include image.html url="/img/application_atmega.jpg" description="" %}

It's possible to solder each pin individually, but you will probably have to use the copper wick to pull off excess solder.

Here is the schematic and layout of the board. The three 2x2 headers are for Bluetooth, input and output.

{% include image.html url="/img/application_schematic.jpg" description="" %}
{% include image.html url="/img/application_layout.jpg" description="" %}

And here is the final board, with most of the parts laid out:

{% include image.html url="/img/application_final_board.jpg" description="" %}

I am using a [DS18B20 one-wire sensor](https://www.sparkfun.com/products/245) to read temperature. This is a "one-wire" device, which means that its information is sent along a single data wire (with a VCC and GND wire as well). I found this guide helpful for figuring out how to read the temperature into the ATMega: [http://www.hacktronics.com/Tutorials/arduino-1-wire-address-finder.html](http://www.hacktronics.com/Tutorials/arduino-1-wire-address-finder.html).

### Application

I am using [Meteor](http://www.meteor.com) to write my controller application. The great thing about Meteor is that you can write JavaScript which runs in the browser AND on Android/iOS. That means that you can use the same code base for all platforms. Meteor's mobile support uses Cordova to run JavaScript inside a native app container.

Luckily, I found a Cordova "plugin" ([https://github.com/don/BluetoothSerial](https://github.com/don/BluetoothSerial)) which implements BLE support in Cordova. This allows me to use a JavaScript API to speak with the Bluetooth device. For example, calling `bluetoothSerial.search()` returns a list of BLE devices. The author of the plugin went through the work of implementing this command on Andriod, iOS, and Windows Phone.

Here are some screenshots of the app:

{% include image.html url="/img/application_connecting.jpg" description="The app finds BLE devices and lets the user connect to any device." %}

{% include image.html url="/img/application_app.jpg" description="The home screen of the app. The user can set a target temperature and duration of a recipe." %}

Finally, here is the whole system. We connect to the BLE device, and output the temperature that is read from the sensor.

<iframe style="display:block; margin: 0 auto; padding-bottom: 30px" width="630" height="473" src="//www.youtube.com/embed/U8Z_sUV4sXE" frameborder="0" allowfullscreen></iframe>


