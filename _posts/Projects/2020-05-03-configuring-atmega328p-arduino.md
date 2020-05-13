---
layout: post
title:  "Configuring an ATMEGA328P to Replace an Arduino Uno"
date:   2020-05-03
excerpt: "Bare minimal setup of an Arduino Uno"
categories: [Projects]
image: "/images/projects/configuring-atmega328p-arduino/cover.jpg"
---
## Rationale
This project was part of an even bigger project which I'm not allowed to talk about yet. But essentially, we wanted to use the ATMEGA328P instead of the Arduino Uno. For those who do not know the Arduino Uno uses the ATMEGA328P microcontroller. So we wanted to just use the microcontroller as is for a few reasons - cheaper and more power efficient.

This configuration allows programming of the ATMEGA328P using the Arduino IDE with almost no restrictions

## Requirements
- Arduino Uno with existing ATMEGA328P
- ATMEGA328P
- Wires (Either use jumper wires or solid core hookup wires which require wire strippers, pliers and cutters)
- Breadboard
- Small flathead screwdriver
- Multimeter
- 0.1 uF ceramic capacitors (Labelled 104 usually)
- 10k resistor
- LEDs (for debugging if you want)
- Arduino IDE

## Breadboard Setup
First, let's connect the ATMEGA to our breadboard. Wire the breadboard as per below.

<img class="image normal" src="/images/projects/configuring-atmega328p-arduino/breadboard-wiring.jpg" alt>

## Burn the Bootloader
Most ATMEGA328Ps do not already have the bootloader installed so we need to burn the bootloader on to the ATMEGA328P.   

Below is the pinout for the ATMEGA328P. Note that pin 1 is denoted by a small dot indentation on your ATMEGA328P 

<img class="image normal" src="/images/projects/configuring-atmega328p-arduino/atmega-pinout.jpg" alt>

Connect the Arduino Uno to the breadboard as per below.

Arduino Uno Pin | ATMEGA328P Pin 
--- | --- 
D9 | 9 (Uses internal 8MHz clock)
D10 | 1 (Reset)
D11 | 17
D12 | 18
D13 | 19
5V and GND | Refer Below

You can choose to use the Uno's 5V and GND to power the ATMEGA328P or use an external power source with a 5V regulator. Just connect the 5V wire to the red circle and GND to the black circle as shown below.

<img class="image normal" src="/images/projects/configuring-atmega328p-arduino/breadboard-wiring-with-power-circle.png" alt>

Now you may connect your Uno to your computer. Using [Atmega_Board_Programmer](https://github.com/nickgammon/arduino_sketches/tree/master/Atmega_Board_Programmer), we can now upload the bootloader. In your Arduino IDE under Tools, set the following:

Tools | Setting 
--- | --- 
Board | Arduino Uno
Programmer | Arduino as ISP

Upload the [Atmega_Board_Programmer](https://github.com/nickgammon/arduino_sketches/tree/master/Atmega_Board_Programmer) to your Arduino Uno and open the serial monitor using `ctrl-shift-M`. Remember to change your baud rate at the bottom right of the serial monitor to 115200. If you saw giberish, reset your Uno via the rest button (beige color) after changing the baud rate. When prompted `Type 'L' to use Lilypad (8 MHz) loader, or 'U' for Uno (16 MHz) loader ...`, enter `L` in the field at the top of the serial monitor and press Enter. Then enter `G` to burn the bootloader. At the end of it, you should see `No errors found` in one of the lines.

This completes the burning of the bootloader. You may disconnect the wires stated in this section.

## Uploading Code
We can now use the Arduino Uno simply for debugging and uploading purposes. However, to ensure the code is uploaded into the ATMEGA328P instead of the Uno, we need to remove the ATMEGA328P inside the Uno. Please make sure that you are using a similar model as mine in which you can remove it (Refer to Arduino Uno image below). If not, I strongly suggest you invest in an FTDI cable. I also highly DO NOT recommend plugging the ATMEGA328P in and out of the Uno.

<img class="image normal" src="/images/projects/configuring-atmega328p-arduino/arduino-uno-with-atmega.jpg" alt>

The best way to remove it is exceedingly disgusting. Use a flathead screwdriver as a lever and slowly pry out the ATMEGA328P. Do it on both sides bit by bit, taking care not to bend the pins. Once one side is entirely out, hold on to that side using your hand and use the screwdriver to pull out the remaining pins. Clearly, no onel ikes to do this, so if you're gonna do it multiple times, use a ZIF.

Connect the Arduino Uno to the breadboard as per below.

Arduino Uno Pin | ATMEGA328P Pin 
--- | --- 
RESET | 1
RX (D0) | 2
TX (D1) | 3
5V and GND | Same as Bootloader Burning

<img class="image normal" src="/images/projects/configuring-atmega328p-arduino/upload-wiring.jpg" alt>

Set the following IDE settings.

Tools | Setting 
--- | --- 
Board | Arduino Pro or Pro Mini
Processor | ATMEGA328P (3.3V, 8MHz)
Programmer | Arduino as ISP

For the processor, note that the frequency matters, not the voltage.

Now you can test a blink code by uploading the following code:
```cpp
void setup() {
  pinMode(8,OUTPUT);
}

void loop() {
  digitalWrite(8,HIGH);
  delay(1000);
  digitalWrite(8,LOW);
  delay(1000);
}
```

You can test it is working by connecting the red pin of the multimeter to the pin 14 (last pin on the same row as pin 1) and the black pin of the multimeter to GND. Watch as the voltage increases and decreases. You may also add an LED if you would like, connecting it to the same pin and GND.

To confirm it is definitely working, you can change the delays.

## Conclusion
So that is how we can create a more minimal setup to replace the Arduino Uno. This saves power and costs if you intend to have multiple setups. In our use case, we needed roughly 30 microcontrollers to work together, so spending $20 on a single Uno and a couple of dollars on each ATMEGA328P definitely saved us a lot of our budget.

I've also compiled all my references in case you wanted to read up a bit more, or do things a little differently. Nick Gammon's guide was my primary source of information though the instructions tend to be a bit long due to the large coverage of his article.

## Resources
<http://www.gammon.com.au/breadboard><br>
<https://www.youtube.com/watch?v=Sww1mek5rHU><br>
<https://www.youtube.com/watch?v=dpgcBsl9D4k>
