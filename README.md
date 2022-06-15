# SomfyIO ESPHome HomeAssistant
Personal project to automate screens using SomfyIO protocol

Using a Somfy remote connected to an ESP8266 with ESPHome software we can now control the Somfy screens of the house.

<div align="center">
  <kbd>
    <img src="images/SomfyIO_ESPHome_AGvdBerg_7-Final board_4.jpg" />
  </kbd>
</div>

## Description

Somfy (screens) have [two communication protocols](https://www.somfy.nl/over-somfy/technologieen-en-compatibiliteit); RTS using RFX-433,92 Mhz and [IO-homecontrol](https://www.somfy.nl/ondersteuning/somfy-motoren/io-homecontrol) using encrypted communication. When I bought my screens I choose SomfyIO. My house has three screens; each with it's own remote.
Currently it is not possible to communicate directly (to pretend using a remote).

With the help of may examples but most important [Beejayf's project SomfyDuino on Hackster.io](https://www.hackster.io/beejayf/somfyduino-io-3d8283) I bypassed the protocol using an original 4-channel-remote.
The way it works is that pussing a button on the remote is faked by connecting wires to the remote-buttons.
The wires are reused from an old desktop-PSU; enough wires combined in a bundle.

I had a WemosD1 mini pro laying around so it could be connected to some IO-pins of this ESP8266 board.
ESPHome is used to program the ESP; it was easy to implement and has OTA (Over The Air) updates.

In my house I plan to put a domotica-sensor in each room, so I add some other sensors to the ESP; light-resistor, an RGB-led, PIR and a buzzer.

Using ESPHome on the ESP has the advantage it is easy connected to my domotica-software HomeAssistant. In HomeAssistant I made some automations and scripts to push a message to the ESP. 

### Needed

- Somfy remote 				(like the Somfy SITUO 5 IO Pure)
- cable with 6 wires		(i cut it from an old Desktop-PSU)
- ESP8266 or ESP32    		(like the WemosD1 mini Pro)
- [ESPHome software](https://esphome.io/)  		(to program the ESP8266)
- [HomeAssistant software](https://www.home-assistant.io/)	(to control the screens, on any hardware)
- Solder board, tin, solder iron
- 4 20Ohm resistors
- 4 small & simple LED's 
- 6 wire connectors with screw (to connect the cable to the solderboard)
- extra hardware like an RGB-led, PIR and buzzer are optional off course

### The correct remote

The old remote, the Somfy SITUO 5 IO Pure is easier to solder wires to.
The new remote has smd components were soldering wires is more advanced
The remote had 4 channels. There are SMD-leds on the remote (on th eleft upper corner) to give information which screen is adressed.
My 3 screens are each equiped with an SITUO 1 IO Pure-remote, for each room. They are not used in this project.

### Pictures of the remote

<div align="center">
  <kbd>
    <img src="images/Somfy-SITUO-5-IO-PURE-L.jpg" />
  </kbd>
    
  Somfy SITUO 5 IO Pure
</div>

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
    
  Newest remote has SMD = much harder to solder
</div>

### Prototype board with WemosD1 mini Pro

- ESP8266
- Wifi-access
- Enough IO-ports

### Connections 

#### Somfy connections

 - Somfy Battery(+)  to ESP - 3V3
 - Somfy Battery(-)  to ESP - GND
 - Somfy "SELECT"    to ESP - GPIO03 = RX
 - Somfy "UP"        to ESP - GPIO14 - D5
 - Somfy "My" / Hold to ESP - GPIO12 - D6
 - Somfy "DOWN"      to ESP - GPIO13 - D7
 
 Hardware each pin is 'active high' and not only connected to somfy but also by small blue LED and xx Ohm resistor to GND
 In ESPHome each the four pins gets a switch, each switch becomes a button
 By activating the button the pin & switch is set low for 500ms and then set high again...thus simulating a short hardware button press on the remote
 In HomeAssistant we see all switches and buttons
 
#### Other connections

RGB LED (PL9823)
 - (short) pin 1 Data In (DI) to ESP - GPIO00 = D3
 - (short) pin 2 V+ (5V)      to ESP - 5V
 - (long)  pin 3 GND          to ESP - GND
 - (long)  pin 4 Data Out (DO) not connected
 - has chipset WS2811
 - In ESPHome 'fastled_clockless' needed & old framework 2.7.3 needed
 
Internal LED (in WemosD1 mini Pro)
 - connected to GPIO02 = D4
 
LDR-restistor
 - one pin of LDR   is connected to ESP - A0 (Analog input)
 - other pin of LDR is connected to ESP - GND
 - A0 is with 20kOhm resistor connected to ESP - 3V3
 - This is a voltage-divider. Max input of A0 is 1V
 
PIR (motion)
 - pin 1 UI = OUT to ESP - GPIO16 = D0
 - pin 2 GND      to ESP - GND
 - pin 3 VCC      to ESP - 3V3
 
Buzzer (passive buzzer, connected to software PWM)
 - + to ESP - GPIO15 = D8
 - - to ESP - GND
 - In ESPHome and HomeAssistant addressed by RTTTL (=Ring Tone Text Transfer Language)
 

### ESPHome software

The WemosD1 mini pro with ESP8266-chip has ESPHome firmware installed
Pins are connected to a 'switch' and made into a 'button'
Besides the code for the somfy pins there is also code for all the other pins like the RGB led, internal led, PIR and Buzzer
Furthermore ESPHome gives extra information like uptime and wifi information

Initialization part to display nice names ESPHome -> HomeAssistant -> user
```
substitutions:
  devicename: Somfy
```


Part to make switch connected to hardware pin
```
switch:
- platform: gpio
  pin: 3  #GPIO3 / RX - connected to Somfy CHANNEL
  id: pin3
  restore_mode: ALWAYS_ON   # start HIGH
- platform: gpio
  pin: 14  #GPIO14 / D5 - connected to Somfy UP
  id: pin14
  restore_mode: ALWAYS_ON   # start HIGH
- platform: gpio
  pin: 12  #GPIO12 / D6 - connected to Somfy STOP
  id: pin12
  restore_mode: ALWAYS_ON   # start HIGH
- platform: gpio
  pin: 13  #GPIO13 / D7 - connected to Somfy DOWN
  id: pin13
  restore_mode: ALWAYS_ON   # start HIGH
```

and
```
# switch functionality
# LED is on if switch is turned off !
- platform: template
  name: ${devicename} CHANNEL-RX-GPIO3  # change name later to functionality
  optimistic: yes
  id: pinnetje3
  turn_on_action:
  - switch.turn_off: pin3   # negative: pin=off => output=low (=> test-led=off)
  turn_off_action:
  - switch.turn_on: pin3    # pin=on => output=high  (=> test-led=on)
- platform: template
  name: ${devicename} UP-D5-GPIO14   # change name later to functionality
  optimistic: yes
  id: pinnetje14
  turn_on_action:
  - switch.turn_off: pin14   # negative: pin=off => output=low (=> test-led=off)
  turn_off_action:
  - switch.turn_on: pin14    # pin=on => output=high  (=> test-led=on)
- platform: template
  name: ${devicename} STOP-D6-GPIO12   # change name later to functionality
  optimistic: yes
  id: pinnetje12
  turn_on_action:
  - switch.turn_off: pin12   # negative: pin=off => output=low (=> test-led=off)
  turn_off_action:
  - switch.turn_on: pin12    # pin=on => output=high  (=> test-led=on)
- platform: template
  name: ${devicename} DOWN-D7-GPIO13   # change name later to functionality
  optimistic: yes
  id: pinnetje13
  turn_on_action:
  - switch.turn_off: pin13   # negative: pin=off => output=low (=> test-led=off)
  turn_off_action:
  - switch.turn_on: pin13    # pin=on => output=high  (=> test-led=on)

```

Emulate a button
 - conform [explanation in ESPHome documentation](https://esphome.io/components/button/index.html)
 
```
# button functionality
# Button is pushed low for 0,5 sec
button:
  - platform: template
    name: ${devicename} Button-CHANNEL-RX-IO3
    id: wemosd1_rx_button
    icon: "mdi:gesture-tap-button"
    on_press:
      - logger.log: "Knop CHANNEL ingedrukt = output RX even LOW = CHANNEL"
      - switch.turn_off: pin3   # negative: pin=off => output low
      - delay: 500ms 
      - switch.turn_on: pin3    # pin=on => output high
  - platform: template
    name: ${devicename} Button-UP-D5-IO14
    id: wemosd1_d5_button
    icon: "mdi:gesture-tap-button"
    on_press:
      - logger.log: "Knop UP ingedrukt = output D5 even LOW = UP"
      - switch.turn_off: pin14   # negative: pin=off => output low
      - delay: 500ms 
      - switch.turn_on: pin14    # pin=on => output high
  - platform: template
    name: ${devicename} Button-STOP-D6-IO12
    id: wemosd1_d6_button
    icon: "mdi:gesture-tap-button"
    on_press:
      - logger.log: "Knop STOP ingedrukt = output D6 even LOW = STOP"
      - switch.turn_off: pin12   # negative: pin=off => output low
      - delay: 500ms 
      - switch.turn_on: pin12    # pin=on => output high
  - platform: template
    name: ${devicename} Button-DOWN-D7-IO13
    id: wemosd1_d7_button
    icon: "mdi:gesture-tap-button"
    on_press:
      - logger.log: "Knop DOWN ingedrukt = output D7 even LOW = DOWN"
      - switch.turn_off: pin13   # negative: pin=off => output low
      - delay: 500ms 
      - switch.turn_on: pin13    # pin=on => output high
```

### HomeAssistant code

- entities from ESP visable
- push button - watching the remote
- scripts to 'push' more buttons in a row
- automation when to lower the screens

## Development to a working situation

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

- Beejayf's project SomfyDuino
     - [on arduino.cc](https://create.arduino.cc/projecthub/beejayf/somfyduino-io-3d8283)
	 - [on Hackster.io](https://www.hackster.io/beejayf/somfyduino-io-3d8283)
- [Tim Alston on twitter (only some pictures)](https://mobile.twitter.com/TimAlston/status/1340330866023813120)
- [Mention on dutch 'Tweakers' forum of Beejayf's SomfyDuino](https://gathering.tweakers.net/forum/list_messages/1982854)
- [First try and asking for help of Celaeno1 & Hakan using OpenHab](https://community.openhab.org/t/solved-somfy-io-rollershutter-motors-which-system-is-best-for-using-with-oh2/81929/12)
- [Github repro of smslabsbr for SomfyRTS](https://github.com/dmslabsbr/esphome-somfy)
- [Github repro of imicknl for connection with the use of Tohama](https://github.com/imicknl/ha-tahoma)

### To-do in this Github explaination

- [x] pinout connection 
- [x] ESPHome code
- [x] References to found examples
- [ ] more explaining code, readable story to new user
- [ ] upload yaml files
- [ ] share this github on social media 
- [ ] share dutch version on [personal website (dutch)](www.ecozonnewoning.nl)

### To-do

- [x] Working with a prototype-board
- [ ] ~~Redesign and use an dedicated pcb~~
- [ ] ~~Temperature & Humidity sensor~~
- [ ] Design and print a nice 3D case


### License

This project is licensed under the [MIT License](LICENSE.md).

## Acknowledgments

Inspiration, code snippets, etc.
 * [README copied from Michael Stickels README template](https://github.com/MichaelStickels/README_Template)
 * [Adapted from TINY README](https://gist.github.com/noperator/4eba8fae61a23dc6cb1fa8fbb9122d45)

