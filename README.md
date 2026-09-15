IoT-Based EB Line Monitoring, Fault Detection & Electricity Theft Detection

A 12V DC prototype using ESP32, three 0--25V voltage sensors, one
ACS712 current sensor, a PN2222A relay driver, one relay, and a local
HTML/WebSocket dashboard.

Features

Real-time Pole A, Pole B and Pole C voltage monitoring

Current monitoring using ACS712

Line-cut detection between A-B and B-C

Fault-location identification

Low-voltage detection

Possible electricity-theft/no-load indication

Relay OFF only for detected line-cut faults

Local web dashboard hosted by ESP32

Wi-Fi + WebSocket real-time data

Voltage/current graph capability

System Block Diagram

12V DC SUPPLY
      |
      v
ACS712 CURRENT SENSOR
      |
      v
    POLE A -------- POLE B -------- POLE C -------- LOAD
      |               |               |
  Voltage Sensor  Voltage Sensor  Voltage Sensor
      |               |               |
      +---------------+---------------+
                      |
                    ESP32
                 /          \
                /            \
             Relay           Wi-Fi
           Driver              |
             |                 v
           Relay       Local HTML Dashboard
             |             WebSocket
             v
            LOAD

Components

Component                            Qty Purpose

ESP32 Dev Board                        1 Controller + Wi-Fi
0--25V DC Voltage Sensor               3 Pole A/B/C voltage
ACS712                                 1 Current measurement
5V Relay                               1 Load protection
PN2222A / 2N2222A                      1 Relay driver
1N4007 diode                           1 Flyback protection
12V DC 2A supply                       1 Prototype supply
LEDs                                   3 Pole indication
4.7kΩ resistors                        3 LED current limiting
Breadboard                             1 Prototype
Jumper wires                 As required Wiring

ESP32 Pinout

Function                    ESP32 GPIO

Pole A voltage sensor S         GPIO34
Pole B voltage sensor S         GPIO35
Pole C voltage sensor S         GPIO32
ACS712 OUT                      GPIO33
Relay driver input              GPIO25

Fault Logic

Condition                            Dashboard Message          Relay

A ≈ 12V, B ≈ 12V, C ≈ 12V            SYSTEM NORMAL              ON
A OK, B ≈ 0V                         LINE CUT BETWEEN A-B       OFF
B OK, C ≈ 0V                         LINE CUT BETWEEN B-C       OFF
Voltage below configured threshold   LOW VOLTAGE                ON
Voltage present + current ≈ 0A       POSSIBLE THEFT / NO LOAD   ON

Current ≈ 0A with voltage present cannot by itself prove electricity
theft; it is only a prototype indication.

Voltage Sensor

The project uses 0--25V DC voltage sensor modules. The sensor output
must remain within the ESP32 ADC input limit. Verify the exact divider
ratio and module output before wiring.

ACS712

The ACS712 is placed in series with the monitored supply path:

12V + --> ACS712 --> Pole A --> Pole B --> Pole C --> Load

The ACS712 module is powered according to its datasheet. Its analog
output may reach 5V, so use suitable voltage scaling/conditioning before
connecting OUT to an ESP32 ADC input.

Relay Driver

ESP32 GPIO25
     |
   Base resistor
     |
     B
   PN2222A
     C -------- Relay coil -------- +5V
     E
     |
    GND

Place a 1N4007 flyback diode across the relay coil:

Cathode -> +5V
Anode   -> transistor collector / coil low side

Check the transistor pinout for the exact manufacturer/package because
PN2222A/2N2222A pin arrangements can differ.

Web Dashboard

The ESP32 runs:

HTTP server: port 80

WebSocket server: port 81

After uploading the firmware:

Open Serial Monitor at 115200 baud.

Note the ESP32 local IP address.

Connect the computer/phone to the same Wi-Fi.

Open:

http://ESP32_IP_ADDRESS

The dashboard displays:

Pole A voltage

Pole B voltage

Pole C voltage

Current

System status

Fault location

Relay status

Real-time graph

Example WebSocket Data

{
  "A": 12.0,
  "B": 11.9,
  "C": 0.0,
  "I": 0.42,
  "msg": "LINE CUT BETWEEN B-C"
}

Testing

Normal

A = 12V
B = 12V
C = 12V

Expected:

SYSTEM NORMAL
Relay ON

A-B Wire Cut

A = 12V
B = 0V
C = 0V

Expected:

LINE CUT BETWEEN A-B
Relay OFF

B-C Wire Cut

A = 12V
B = 12V
C = 0V

Expected:

LINE CUT BETWEEN B-C
Relay OFF

Possible Theft / No Load

Voltage present
Current approximately 0A

Expected:

POSSIBLE THEFT / NO LOAD
Relay ON

Recommended GitHub Structure

EB-Line-Monitoring/
├── README.md
├── ESP32/
│   └── EB_Line_Monitoring.ino
├── Web/
│   └── dashboard.html
├── Circuit/
│   ├── circuit-diagram.png
│   └── block-diagram.png
├── Documentation/
│   └── Project-Report.pdf
└── Images/
    ├── prototype.jpg
    └── dashboard.jpg

Software

Arduino IDE

ESP32 Arduino Core

WiFi

WebServer

WebSockets

HTML

CSS

JavaScript

Safety

This repository is for a 12V DC educational prototype only. Do
not connect this circuit directly to 230V/415V utility mains. For a
real EB application, use properly rated isolated voltage/current
sensors, protection devices, certified switching equipment, fusing,
enclosure, and professional electrical engineering.

Future Scope

More poles and automatic fault-section identification

Multiple current sensors

Energy/power measurement

Historical data logging

Fault timestamps

GPS fault location

SMS/Telegram alerts

Dashboard authentication

Industrial-grade AC sensing and protection

Author

Arun Prasanth
Electronics and Communication Engineering
Embedded Systems / Firmware Project
