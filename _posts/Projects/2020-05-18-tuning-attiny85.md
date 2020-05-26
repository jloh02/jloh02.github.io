---
layout: post
title:  "Tuning the ATTINY85"
date:   2020-05-17
excerpt: "Calibrating the perfect clock rate for the ATTINY85 crystal oscillator"
categories: [Projects]
image: "/images/projects/tuning-attiny85/cover.jpg"
---
## Introduction
Whilst trying to get SoftwareSerial to work between multiple ATTINY85s, I found myself in the face of inconsistent gibberish. I would receive decent data for about 4s and everything after would print unprintable characters. As such, I suspected it was to do with the crystal oscillator. Without much resources due to the lockdown, I could only rely on my trusty (not really) serial monitor. This guide shows the method I used to get all the crystal oscillators tuned.

## Background Information
According to the [SpenceKonde ATTinyCore GitHub](https://github.com/SpenceKonde/ATTinyCore), the internal crystal oscillators are factory calibrated up to +/-10% which works for most cases such as regular printing to serial monitor so you probably won't ever need to touch this part of the ATTINY85. But you most likely need to calibrate it further for anything to do with precise timing.

The internal oscillators can be tuned by changing the `OSCCAL` register. For example, we can simple set `OSCCAL = 100`

## Code
We can do a simple brute force approach. Since it is factory callibrated to +/-10%, our ideal point should be within 10%. But we want to find the working bounds of it as well (values which are slightly higher or lower than perfect can print correct results). To find both lower and upper bounds, we check from 80% to 120% of the orignal OSCCAL. An extra arbitrary tolerance of +/-5 was added to make sure we get both bounds.

```cpp
#include <SoftwareSerial.h>

#define comRx 3
#define comTx 4

#define PRINT_TIMES 1000
#define Serial compSer

SoftwareSerial compSer = SoftwareSerial(comRx, comTx);

void setup() {
  Serial.begin(9600);

  double osc = OSCCAL;
  Serial.print("Original OSCCAL = ");
  Serial.println(OSCCAL);
  delay(5000);
  
  for(int i=floor(osc*0.8)-5;i<ceil(osc*1.2)+5;i++){
    OSCCAL = i;
    for(int j=0;j<PRINT_TIMES;j++){
      Serial.print("OSCCAL = ");
      Serial.println(i);
    }
  }
}

void loop() {}
```

The above code prints OSCCAL 1000 times to verify that a certain OSCCAL value works. Based on experience, about 500 should also be sufficient but 1000 is just to be very certain. Do not be alarmed that you see huge amounts of "alien language". That just means whatever value that is, it is not nearly suitable to be the OSCCAL

## Obtaining the Optimal OSCCAL
The above script takes about 10-15 minutes to run. You can go and have a coffee break in the meantime. If you'd like to accelerate the process by staring at the screen, go ahead. Stare at the screen until you don't see any abnomalies and record that as your lower bound. Once you see abnomalies again, record the previosu OSCCAL as your upper bound. Anomalies are any single value in which the line break is not printed or a character is printed wrongly. Once you have your bounds, `Optimal OSCCAL = Average of Bounds = (Upper+Lower)/2`.

Alternatively, we could copy-paste the entire serial monitor output into a text file and run the python script below to find the optimal OSCCAL which does a simple count of `OSCCAL = <value>\n`. It first prints an array with OSCCAL values suitable for printing at 9600 baud rate. Assuming you printed enough values to have reliable results, it would also print an optimal OSCCAL value using the start and end of the array as your bounds.

```python
import re
import math

f = open('osccal_output.txt','r',encoding="utf8")
PRINT_TIMES = 1000

osc = int(re.search(r'Original OSCCAL = (\d+)',f.readline()).group(1))
s = f.read()
allowed = []
for i in range(math.floor(osc*0.8)-5,math.ceil(osc*1.2)+5):
    if len(re.findall(r'OSCCAL = {}\n'.format(i),s)) == PRINT_TIMES: 
        allowed.append(i)
print('OSCCAL values with {} correct prints:'.format(PRINT_TIMES),allowed)
if len(allowed) == allowed[-1]-allowed[0]+1:
    print('Optimal OSCCAL is',(allowed[0]+allowed[-1])/2)
```

## Making your OSCCAL Permanent
You can simply save this optimal OSCCAL value into the EEPROM using the [Arduino EEPROM Libary](https://www.arduino.cc/en/Reference/EEPROM) and read from EEPROM memory each time you start the code.

## Conclusion
Of course, this is not the best method to tune the internal oscillator of the ATTINY85. If you'd like to cut some costs or do not have access to an oscillator, this method to tune the ATTINY85 is sweet and simple. At least it can get a pretty decent OSCCAL with lower variance than factory manufactured.