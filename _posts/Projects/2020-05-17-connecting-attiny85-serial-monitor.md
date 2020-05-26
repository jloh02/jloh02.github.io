---
layout: post
title:  "Connecting your ATTINY85 to a Serial Monitor"
date:   2020-05-17
excerpt: "Getting your favourite Hello World program on the ATTINY85"
categories: [Projects]
image: "/images/projects/connecting-attiny85-serial-monitor/cover.jpg"
---
## Introduction
This is a continuation to the [previous guide on the ATTINY85](https://jloh02.github.io/projects/downscaling-arduino-attiny85/). I didn't realise that printing the simple "Hello World" would be this annoyingly difficult, so this is a easier guide.

## Requirements
Finished bootloading and uploaded a simple program ATTINY85. Note that this guide uses the [SpenceKonde ATTinyCore](https://github.com/SpenceKonde/ATTinyCore).

## Procedure
This is really simple but there are important steps you have to take to ensure that everything works. The SpenceKonde ATTinyCore does support existing software serial on digital pins 0 and 1 using the `Serial` keyword. However, most of the time we want more versatility so I'll be going through how to use any pin.

First, we upload the following code which is a simple Hello World configured with SoftwareSerial using the `SoftwareSerial.h` library. Note that I named the serial port as `serial` and not `Serial` to avoid conflict of keywords. It is also important that the pins chosen for RX and TX (3 and 4) are the corresponding pins on the Arduino, not the pin number of the ATTINY85.

```cpp
#include <SoftwareSerial.h>

#define rx 3 
#define tx 4
String s = "Hello World!";

SoftwareSerial serial(rx, tx);
void setup() {
  serial.begin(9600);
}

void loop() {
  serial.println(s);
}
```

We can upload this code normally as per the [previous guide](https://jloh02.github.io/projects/downscaling-arduino-attiny85/). To check that it works, you can plug in the TX pin of the ATTINY85 to the RX pin of the Uno (Digital pin 0). You should see the RX LED light up on the Uno. You'd notice that the Serial Monitor will be filled with garbage or not print anything at all.

## The Solution
This is the part that many guides do not contain. The `ArduinoISP` code interferes with our Serial pins so we're going to have to upload a blank code. This can be achieved by creating a new file and uploading it to the **Arduino Uno**, or use the BareMinimum example under `File > Examples > Basics > BareMinimum`. You must now connect your RX to RX and TX to TX. (Remember: **You cannot upload any code while RX or TX are connected to the Uno**) You can now open your Serial Monitor and marvel at the effort it took to print "Hello World".

## Tuning of ATTINY85 Oscillators
In the slight chance that your serial monitor prints rubbish, or in case you want to use the SoftwareSerial libraries to communicate between multiple ATTINY85s, you're gonna need to tune the crystal oscillators. The internal crystal oscillators for the ATTINY85 are calibrate to a +/-10% which may result in the gibberish you are receiving. This is due to the difference in timings. My braindead method for tuning that can be found [here]().