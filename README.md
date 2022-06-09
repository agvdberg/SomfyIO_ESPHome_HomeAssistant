# SomfyIO ESPHome HomeAssistant
Personal project to automate screens using SomfyIO protocol

Using a Somfy remote connected to an ESP8266 with ESPHome software we can now control the Somfy screens of the house.

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_7-Final board_4.jpg" />
  </kbd>
</div>

## Description

Somfy (screens) have two communication protocols; RTS using RFxxx and SomfyIO using encrypted communication. When I bought my screens I choose SomfyIO. My house has three screens; each with it's own remote.
Currently it is not possible to communicate directly (to pretend using a remote).

With the help of an example of [xx on Hackster](website) I bypassed the protocol using an original 4-channel-remote.
The way it works is that pussing a button on the remote is faked by connecting wires to the remote-buttons.
The wires are reused from an old desktop-PSU; enough wires combined in a bundle.

I had a WemosD1 mini pro laying around so it could be connected to some IO-pins of this ESP8266 board.
ESPHome is used to program the ESP; it was easy to implement and has OTA (Over The Air) updates.

In my house I plan to put a domotica-sensor in each room, so I add some other sensors to the ESP; light-resistor, an RGB-led, PIR and a buzzer.

Using ESPHome on the ESP has the advantage it is easy connected to my domotica-software HomeAssistant. In HomeAssistant I made some automations and scripts to push a message to the ESP. 

### The correct remote

- new version smd = hard to solder
- old version better to solder
- 4 channels, 3 screens. fifth setting ALL (not used)
- SMD leds on remote giving channel information

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_4-Zelf solderen_2.jpg" />
  </kbd>
    
  Remote with soldered wires
</div>

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_3-Betere remote_1.jpg" />
  </kbd>
    
  Back of the remote: Somfy Situo 5 io Pure
</div>

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_4-Zelf solderen_1.jpg" />
  </kbd>
    
  Remote unpacked from case
</div>

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_1-Remote te modern_1.jpg" />
  </kbd>
    
  Newest remote: Somfy Situo 5 IO
</div>

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_1-Remote te modern_1.jpg" />
  </kbd>
    
  Newest remote has SMD = much harder to solder
</div>

### Prototype board with WemosD1 mini Pro

- ESP8266
- Wifi-access
- Enough IO-ports
- 'make connection to GND' -> normal HIGH = 3.3V
- normal HIGH = extra resistor & blue LED
- extra sensors; LDR, RGB, PIR & Buzzer (no temp & hum this time)

### ESPHome software

- generated from within HomeAssistant
- all sensors connected
- to remote: switch + button
- extra status information: uptime, wifi info

### HomeAssistant code

- entities from ESP vissable
- manual use button = 500m low
- scripts to 'push' more buttons in a row
- automation when to lower the screens

## Getting started

### Breadboard try-out

- install everything on a breadboard
- add software on ESP and automations in HomeAssistant

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_5-Breadboard opbouw_1.jpg" />
  </kbd>
    
  Breadboard with somfy-wire-connectors already removed
</div>


### Update to prototype board

- plan
<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_6-Solderen_1.jpg" />
  </kbd>
    
  Layout of prototype board
</div>

 - components, ESP removable
<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_7-Final board_1.jpg" />
  </kbd>
    
  Prototype board with ESP
</div>

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_7-Final board_2.jpg" />
  </kbd>
    
  Prototype board without ESP
</div>

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_7-Final board_3.jpg" />
  </kbd>
    
  Back of prototype board 
</div>
 
### Troubleshooting

 - screen down after power loss: default at 'all channels'
 - connection lost; LDR=0 -> Calculated Lux=MAX -> trouble with graphics

## Back matter

### Legal disclaimer

Usage of this description for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state, and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this description.

### Acknowledgements

Thanks to all who helped inspire this project

### See also

- [A simple README.md template](https://gist.github.com/DomPizzie/7a5ff55ffa9081f2de27c315f5018afc)
- [A template to make good README.md](https://gist.github.com/PurpleBooth/109311bb0361f32d87a2)
- [A sample README for all your GitHub projects](https://gist.github.com/fvcproductions/1bfc2d4aecb01a834b46)
- [A simple README.md template to kickstart projects](https://github.com/me-and-company/readme-template)

### To-do

- [ ] Still need to do this
- [ ] ~~Decided not to do this~~
- [x] Done!

### License

This project is licensed under the [MIT License](LICENSE.md).

## Acknowledgments

Inspiration, code snippets, etc.
 * [README copied from Michael Stickels README template](https://github.com/MichaelStickels/README_Template)
 * [Adapted from TINY README](https://gist.github.com/noperator/4eba8fae61a23dc6cb1fa8fbb9122d45)

