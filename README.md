# WNKmaster 2000

**This is the WNKmaster soldering station project.**

## Purpose
Weller has some very nice micro soldering tools in the WX, and older WM series.
Sadly, these tools, and especially the soldering stations are hideously expensive.
So, building a DIY soldering station could save quite a bit of money.

There are a few projects that support the older, analog WM series tools, but
availability for those tools is starting to decline. So, I set out to design and
build a soldering station for the WX series tools, and this is the result.
The total BoM cost should be well below $100, even if you order all the parts from Digikey or Mouser.
This does not include the enclosure and mains transformer, as I had those lying around for some time and don't remember what I paid for them at the time.

## Features
- Compatible with Weller WX series tools, like WXMT and WXMP.
- Supports two tools
- Supports 12V and 24V tools (24V untested, though)
- Supports all original Weller tool settings
- Saves tool settings to FAT FS
- Simple, menu driven user interface
- Zero crossing switching if used with a mains transformer
- Hopelessly detuned PID controller

![signal-2024-11-16-180053_002](https://github.com/user-attachments/assets/62338a2f-e7a4-4f7b-8abd-2d32937dafd1)

## Design
I made this in my spare time, often while voluntarily incapacitated, as kind of a "lightning rod" project, where I can make all the mistakes I can't afford to make at work, so expect a lot of them.

### Hardware
The computing part is built around a Raspberry Pi Pico (or Pico 2), because I had one collecting dust, and it has two independent I2C peripherals, which means it supports two tools without going through an external multiplexer. It still might benefit from a bit of input protection on those pins, but here's living dangerously. Also, the original Weller stations run the I2C buses and VDD for the tools at 5V, which I'm not sure is even in spec for the EEPROM and ADC in the tools. Anyway, this thing obviously runs everything at 3.3V, and I haven't had any problems with it yet.

The power stage is more or less a simplified clone of a WX2, which uses a 24V AC mains transformer with center tap, but can be easily modified to work with any power source imaginable, even a USB-C-PD trigger board from Aliexpress. Just ditch the rectifier diodes, and connect your power supply to the 12V and/or 24V rails directly.

The display board accepts a Nokia 5110 display, but with a slightly different pin-out than the ones typically found on google. Be aware of that, or just change the display board according to your needs. The KiCAD files are all there.

### Software
The code is a mess. I really made all this up as I went along. There is no real design, no architecture, no nothing. It started out as something that just tried to make some sense of the ADC readings and put out some PWM. Then I started adding on features without thinking. Also, I sometimes had to abandon the project for weeks at a time, which didn't help, either. 
Expect lots of gratuitously complicated code, memory leaks and access violations.

This being said, it mostly works and can do all the things listed in the features section. The only thing not implemented yet is the *performance* feature. I imagine this to be some kind of *soft start* for the heaters, that will reduce the maximum PWM value when they're cold.

To build it, you need a reasonably recent (tested with 2.3.2) Arduino-IDE, with a Raspberry Pi Pico board package and all the necessary libraries installed, and in `Tools->Flash Size` you need to choose a setting that includes some space for a file system. Doesn't really matter, which.
If you want to use this with anything other than a center tapped AC mains transformer, you also need to set `HEATER_METHOD_PWM` to 1 in `wnk_master.h`.

![signal-2025-01-03-214511_002](https://github.com/user-attachments/assets/d3d8abb5-2d03-4acf-984e-51cd7378ef8b)
![signal-2025-01-03-214511_003](https://github.com/user-attachments/assets/70a2f9d5-fd78-48dd-a848-14c834e9cd68)

## CAD files
The only file that's really necessary to build a functional prototype is `wx_socket-10pins_retention_features.step`. I had mine resin printed at JLCPCB, and they came out perfectly, even though the integrity check complained about wall thickness. That socket will work nicely with ***Amphenol PN: VN0201500521*** contacts. It fits the plug like a glove and doesn't even require any glue to hold the contacts in place, like my earlier prototypes did. Yay!

The rest of the CAD repo contains various development stages of that plug, and lots of other junk. Mainly front and back panel designs for a ***TEKO KL22.9*** enclosure and G-Code that probably won't work with your CNC machine.

![signal-2025-01-03-214511](https://github.com/user-attachments/assets/b06c70e6-ad0e-40c9-b3bf-ef9ca5f48cf8)

## Acknowledgements
This project would have been a lot harder, if not impossible, but no doubt a lot more work, without the work of other people.
These are mainly the people who contributed tear down pictures, reverse engineered schematics, and 3D files to these forum threads:
- https://www.eevblog.com/forum/reviews/whats-inside-weller-wmrt-tweezers/
- https://www.eevblog.com/forum/reviews/weller-wxr-3-rework-station-teardown/
- https://www.mikrocontroller.net/topic/367344 (German)
