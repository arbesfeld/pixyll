---
layout:     post
title:      Final Project
image:      img/final_header.jpg
number:     '14'
---

For my final project I built a sous-vide immersion cooker:

{% include image.html url="/img/final/final_eggs_all.jpg" description="" %}

Sous-vide is a method for cooking food in a temperature-controlled water environment for longer than normal cooking times, at an accurately regulated temperature. Sous-vide cookers are often used in high-end restaurants. In the past few years, however, they have become more popular in household kitchens.

An average sous-vide cooker costs $2000, with low-end cookers going for around $300. I wanted to build my own cheaper version of the device. I also wanted to be able to control it via Bluetooth, so that I could leave the device in my room and control it from anywhere on my floor.

Just want to see the food it can make? [Click here](#results)

# Electronics

The electronics for the cooker are fairly simple. We really are dealing with three components:

* A waterproof temperature sensor: [DS18B20](https://www.sparkfun.com/products/11050)
* A switched AC current via a solid-state relay: [SSR-25da](http://www.amazon.com/Ssr-25da-Temperature-Control-Solid-State/dp/B004HZN628)
* Bluetooth for serial input / output: [BLE Mini](http://www.amazon.com/Bluetooth-4-0-Low-Energy-Mini/dp/B00DJA7HIQ)

I got a chance to play around with the temperature sensor and BLE mini device in [week 11](http://fab.cba.mit.edu/classes/863.14/people/matthew_arbesfeld/2014/12/02/applications/). I also got the solid-state relay working in [week 10](http://fab.cba.mit.edu/classes/863.14/people/matthew_arbesfeld/2014/11/19/output/). This week, I brought all of the components together on a single board.

#### Reading Temperature

Step 1 is to read the temperature of the water. I found a handy device which could do exactly this, a [DS18B20](https://www.sparkfun.com/products/11050):

{% include image.html url="/img/final/final_temp_sensor.jpg" description="A 'one wire' waterproof temperature sensor." %}

This is known as a "one-wire device," because data from the sensor is transferred over a single wire. We also connect a VCC and GND wire, so there are three wires in total. In software, there is a [OneWire Arduino library](http://playground.arduino.cc/Learning/OneWire) which lets you create a `OneWire` bus for data transfer. Dallas Temperature created their own [library](http://milesburton.com/Dallas_Temperature_Control_Library) on top of the OneWire library which provides intuitive commands for reading temperature:

    #include <OneWire.h>
    #include <DallasTemperature.h>

    // Where the temperature sensor probe is connected.
    #define ONE_WIRE_BUS 14

    // Setup a OneWire instance to communicate with any devices.
    OneWire oneWire(ONE_WIRE_BUS);

    // Pass our oneWire reference to Dallas Temperature.
    DallasTemperature(&oneWire);

    void setup() {
      // Start up the library.
      sensors.begin();
    }

    void loop() {
      sensors.requestTemperatures();
      float tempC = sensors.getTempCByIndex(0);
    }

#### Switching AC Current

I used [these guys](http://www.amazon.com/gp/product/B000VK0DRY/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1) to heat water. They're AC powered, so we need a way to turn them on and off via a signal from our microcontroller. The most common way to do this is via a solid state relay.

{% include image.html url="/img/output_ssr_powered.jpg" description="" %}

The green wires are connected to a pinout and GND, and the white wire intersects a link to the heaters. When the pin is turned on, current from the wall socket can flow to the heaters.

#### Bluetooth

The final piece of the puzzle is Bluetooth. I used the [BLE Mini](http://www.amazon.com/Bluetooth-4-0-Low-Energy-Mini/dp/B00DJA7HIQ) device from RedBearLabs. The device is super simple to setup. Since I was using an ATMega which has hardware serial, I simply connected RX/TX to the microcontroller and powered the board. It worked on the first try.

Bluetooth + temperature sensing in action:

<iframe style="display:block; margin: 0 auto; padding-bottom: 30px" width="630" height="473" src="//www.youtube.com/embed/U8Z_sUV4sXE" frameborder="0" allowfullscreen></iframe>

#### Power

I needed an AC -> 5V power adapter. The only thing I had on hand was an iPhone charger, but any ~1A power adapter would do the trick.

#### Board Assembly

Here is the schematic for my board:

{% include image.html url="/img/final/final_schematic.jpg" description="" %}

and the layout:

{% include image.html url="/img/final/final_diagram.jpg" description="" %}

Some points about this design:

* An ATMega328 was required because the libraries are larger than 8kb.
* 2x2 headers provide an interface for input/output devices to the microcontroller
* The board is powered via 5V USB.
* I drilled holes in the board (shown in green) to serve as mounting points.

The only hard part about soldering the board are the small leads on the ATMega.

{% include image.html url="/img/final/final_tape.jpg" description="Tape served as a lever for positioning the chip." %}

{% include image.html url="/img/final/final_soldered_board.jpg" description="The final board, with all soldered components." %}

# Hardware

Since I will be running 120V AC through my circuit, I needed to build an enclosure to house the various components of the device. No one likes electrocution!

#### Design

My tool of choice for the design was SolidWorks. I wanted very precise measurements, because I would be mounting various components to the enclosure. Also, I wanted to be able to define the model parametrically, to allow me to quickly change the design if needed.

{% include image.html url="/img/final/final_solidworks.png" description="The final SolidWorks model." %}
{% include image.html url="/img/final/final_equations.png" description="Equations defining the enclosure." %}

[Download](https://www.dropbox.com/s/yrr9at5zfqvnwgh/sous-vide.SLDPRT?dl=0) the SolidWorks file.

#### Printing

I used the MakerBot Replication 2 with PLA plastic. MakerBot has proprietary software for printing `.obj` and `.stl` files.

{% include image.html url="/img/final/final_maker_ware.png" description="Printing the top of the case in MakerWare" %}
{% include image.html url="/img/final/final_maker2.jpg" description="" %}
{% include image.html url="/img/final/final_maker1.jpg" description="Printing in progress." %}

I was quite pleased with how the parts came out:

{% include image.html url="/img/final/final_top_3d_print.jpg" description="The top half of the enclosure. 10 hour print, 0.33 pounds of PLA." %}
{% include image.html url="/img/final/final_mounting.jpg" description="The bottom half of the enclosure, with mounted power sockets. 3 hour print, 0.25 pounds of PLA." %}

A few interesting points about my enclosure:

* I used a friction-fit sliding mechanism to attach the two parts of the case. Clearance was only 0.01", so I had to do quite a bit of sanding to get the part to fit. Make sure to account for 3d printing artifacts when calculating your tolerances.
* For AC connectors, I used this [female outlet](http://www.amazon.com/gp/product/B008J80J1A/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1) and this [male socket](http://www.amazon.com/gp/product/B00511QVVK/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1) from Amazon. I copied the "mounting size" directly from Amazon, but really should have left some clearance to let the parts slide in more easily.
* Screw mounting works great with PLA plastic. I left ~0.1" holes and hand drilled 6x3/4" screws into the plastic.
* I also built a small power-strip. We power a [circulation pump](http://www.amazon.com/gp/product/B00CXDOQWA/ref=oh_aui_detailpage_o04_s00?ie=UTF8&psc=1) whenever the sous-vide is turned on.
* In order to make the device as general as possible, you can plug in any device which is switched on by the relay. This lets you connect devices other than heaters to the unit. For example, we could control a refrigeration unit with the same device.

# Assembly

Once my 3d prints finished, it was time to assemble everything together!

This was my first time working with AC power, but I learned a few tricks:

* 16 or 18 gauge wire must be used to carry the high AC current.
* Wire nuts work great for attaching multiple wires together.
* Don't forget to solder wires onto the power leads.
* There is nothing wrong with copious amounts of electrical tape.

{% include image.html url="/img/final/final_wiring.jpg" description="Wiring the front face of the enclosure with 16 gauge wire." %}
{% include image.html url="/img/final/final_relay_mount.jpg" description="The solid state relay is mounted on a heatsink which is attached to the enclosure via screws." %}
{% include image.html url="/img/final/final_power_screws.jpg" description="These things work great for connecting multiple wires together." %}
{% include image.html url="/img/final/final_screws.jpg" description="Screwing the PCB into the mount holes." %}
{% include image.html url="/img/final/final_full_wiring.jpg" description="Taken just before closing the enclosure. Everything is tested and working!" %}
{% include image.html url="/img/final/final_assembly.jpg" description="Sliding the top into place." %}
{% include image.html url="/img/final/final_full_assembly.jpg" description="Everything sealed tightly inside. Ready to make some food!" %}

Woo! That was a lot of work.


*My post on [Hacker News](https://news.ycombinator.com/item?id=8753027) garnered a lot of feedback on the AC component of my design. There were safety issues in my original design which should have been addressed. Thank you to [cnvogel](https://news.ycombinator.com/user?id=cnvogel) and [mikeytown2](https://news.ycombinator.com/user?id=mikeytown2).*

- I should have used [crimped terminal connectors](https://www.google.com/search?newwindow=1&q=crimp+terminal+connector&spell=1&sa=X&ei=lJ-PVN70IY-INonTgYgC&ved=0CBwQBSgA&biw=1628&bih=953&dpr=0.9) to connect the wire to the inlet sockets.
- Terminal blocks are safer than screw caps for connecting cables.
- I could have 3d printed a clip that goes over the terminals on the relay.
- Always design enclosures with a physical barrier between the "high voltage" side and the "low voltage" side.
- Running the AC through a device like [this](http://www.homedepot.com/p/Leviton-SmartLockPro-20-Amp-Combo-Slim-Blank-GFCI-Outlet-White-R58-X7590-0KW/202026837) or [this](http://www.functionaldevices.com/building-automation/display.php?model=RIBTE01B) would have made it much safer, especially since we are dealing with water.

# Application Programming

I go into detail about the SousVide controller application in [Week 11](http://fab.cba.mit.edu/classes/863.14/people/matthew_arbesfeld/2014/12/02/applications/). In essence, the app is a cross-platform mobile app that lets you control the temperature and duration of your sous-vide recipes. Some features include:

* Signing in (via Google) to save recipes.
* Notifications when the recipe duration has elapsed
* Preheating the sous-vide, and cooking when ready.
* Monitoring temperature and on/off state of the sous-vide.

{% include image.html url="/img/final/final_app.jpg" description="A screenshot of the app, when cooking food. The blue text indicates that the heaters are switched off (because the current temperature is slightly higher than the target)." %}

All of the code is available on my [GitHub](https://github.com/arbesfeld/Sous-Vide).

# Cost

Parts:

* [Immersion heaters](http://www.amazon.com/gp/product/B000VK0DRY/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1): $7.87 x 3
* [25A SSR](http://www.amazon.com/Ssr-25da-Temperature-Control-Solid-State/dp/B004HZN628/ref=pd_sim_pc_1?ie=UTF8&refRID=1X7PZ5ZH7E72NAX1NNFN) ($4) with [heat sink](http://www.amazon.com/Heatsink-Heat-Solid-State-Relay/dp/B0052EK0D6) ($2.50)
* [BLE Mini](http://www.amazon.com/gp/product/B00DJA7HIQ/ref=oh_aui_detailpage_o05_s01?ie=UTF8&psc=1): $31.99
* [DS1820B sensor](https://www.amazon.com/gp/product/B008HODWBU/ref=oh_aui_detailpage_o05_s00?ie=UTF8&psc=1): $3.47
* [Aquarium pump](http://www.amazon.com/gp/product/B00CXDOQWA/ref=oh_aui_detailpage_o04_s00?ie=UTF8&psc=1): $9.75
* [Inlet male power socket with fuse switch](http://www.amazon.com/gp/product/B00511QVVK/ref=oh_aui_detailpage_o02_s00?ie=UTF8&psc=1): $4.85
* [Female power sockets](http://www.amazon.com/gp/product/B008J80J1A/ref=oh_aui_detailpage_o01_s00?ie=UTF8&psc=1): $2
* [PLA plastic](http://www.amazon.com/gp/product/B00AA5AIXO/ref=oh_aui_detailpage_o00_s00?ie=UTF8&psc=1) (for enclosure): $15
* ATMega328 and other board components: $6

Total cost: ~$100

Overall, this project was much cheaper than buying a conventional sous-vide. Moreover, this device can control any arbitrary AC current based on temperature via Bluetooth. For example, we could use the same device to smoke meats or refrigerate beer.

# Results
<a id="results"></a>

Finally, it's time for the food porn! A sous-vide is only as good as the food it makes:

#### Eggs

My first test was cooking eggs at 76C for 45 minutes:

{% include image.html url="/img/final/final_cooking_eggs.jpg" description="Soft-boiling eggs at 76C." %}
{% include image.html url="/img/final/final_egg.jpg" description="Delicious! The yolk tasted like butter!" %}

#### Hamburger

I cooked a burger at 57C and then seared on the grill for a great medium-rare:

{% include image.html url="/img/final/final_submerged_burger.jpg" description="Using a cup to hold the burger submerged in the liquid. Yea, the food looks kinda gross as it is being cooked." %}
{% include image.html url="/img/final/final_burger.jpg" description="" %}
{% include image.html url="/img/final/final_enjoying_burger.jpg" description="I'm enjoying the burger." %}

#### Steak

Steak is the quintessential sous-vide item. I cooked this boneless ribeye at 56C for 90 minutes.

{% include image.html url="/img/final/final_steak_in_plastic.jpg" description="The steak après sous-vide." %}
{% include image.html url="/img/final/final_whole_steak.jpg" description="Searing the food after sous-vide seals in the juices and makes it look more appetizing." %}
{% include image.html url="/img/final/final_slicing_steak.jpg" description="Medium-rare throughout the whole steak. This is really hard to achieve with conventional cooking techniques." %}
{% include image.html url="/img/final/final_steak_potato.jpg" description="Steak and sweet potato medallions -- the perfect way to celebrate the end of a semester!" %}





