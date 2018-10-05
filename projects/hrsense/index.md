---
layout: default
title: Heartbeat Sensor
---

# Heartbeat Sensor

### Motivation

During my sophomore year, I spent a night in the hospital for unimportant reasons (*cough exhaustion cough*). During my stay, they had me hooked up to a pulse-ox, which also gave a readout for pulse. This became relevant, because apparently there was a built-in alarm that would go off if a patient's pulse dipped below 45. Mine did this whenever I started to fall asleep, which was not helpful for the whole exhaustion thing.

45 is quite low for a heart rate, especially considering I'm not an athlete by any means. I've been wondering since then what my heart rate is like at night, and whether the fact that my HR was so low that night only due to dehydration/exhaustion/starvation/what have you.

So of course I built a sensor to track it for me! Because buying a Fitbit would be too easy.

### Overview

The sensor uses a small piezo to detect heart rate when strapped to my finger. It uses two custom PCBs, worn around the wrist. One is used just for batteries. The other collects the voltage from the piezo, does some basic amplification/thresholding, and triggers a high power infrared LED when a pulse is detected. This infrared flash isn't visible to the human eye, so it won't bother me while I'm trying to sleep, but the signal can be picked up by a modified webcam. Storing 6+ hours of footage and analyzing it manually sounds like an actual punishment, so instead I wrote a basic C++ program using OpenCV to record light intensity in each frame, and then used a combination of C++ and MATLAB to extract pulses from the light intensity data, and plot it across the time I spent asleep.

### Early Design Decisions
The first question I always get whenever I mention this project to anyone is "why not just use an Arduino/microcontroller/Fitbit". As I have to explain every time, this project is intended as a design/learning challenge more than a purely functional device. Of course it would be simple to implement this with an Arduino Nano, but where's the fun in that? Subsequently, I decided to keep all of my circuit elements analog.

Probably the biggest decider of form factor and op-amp circuit design was the fact that I intended this to be a wearable, and thus had to run the project off of batteries. I wasn't a fan of the aesthetic of strapping a 9V battery to my arm, so I decided to go with coin cells, choosing the massive CR2477. In reality, these weren't all that much smaller than the 9V, but they did have a higher capacity, and gave me more flexibility with the voltage (I ended up putting two in series for a ~6V supply). In the future, I'll probably go with smaller coin cells, as I didn't end up running into problems with capacity, and the CR2477's are chunky.

For the sake of not needing even more batteries, I opted to use single supply designs. For those unfamiliar, this means that my op-amp circuits will swing from (approximately) ground to VDD, rather than -VDD to VDD.

### Circuitry

The sensing element is the same type that is often used for guitar pickups or high frequency speakers. It uses the piezoelectric effect, which allows translation between mechanical strain and voltage [details](https://www.nanomotion.com/piezo-ceramic-motor-technology/piezoelectric-effect/). Based on my early experimentation, the piezo had a DC offset, so I first put a decoupling capacitor in line with the piezo, and then biased it to half of the supply voltage. That way, the piezo output was centered at VDD/2, allowing amplification in both "directions" even with the single supply. One note for anyone trying to similar projects: I had to bias the piezo with extremely large resistors (in my case, 6.8MΩ for each resistor). I'm not certain, but I think this has something to do with the high impedance of the sensor itself. In any case, using these massive resistors was uniquely interesting, as they were large enough that the measurements picked up by an oscilloscope were warped, as the probe itself probably had an input resistance of 1MΩ. 

In order to avoid any issues with loading in further stages, the first thing I did was buffer the input through an op amp. 
Next, I amplified the input by a factor of 31 (the pulse amplitude was around 160mV), moving it into a range where I could confidently interact with the signal without worrying about component tolerances. Of course, using this jagged output to flash the LED would have produced extremely noisy, and fast signals. To deal with this, I simply added a comparator set to 70% of the supply, such that a clear pulse would be produced. The output of this comparator was connected to a MOSFET used to switch the LED on and off. Somewhat ironically, this MOSFET came from my first ever parts order for personal projects, yet this was the first time I actually needed a MOSFET. 

### Part Selections


### Modding a webcam to see IR
Humans can only see a very limited portion of the EM spectrum (INSERT DIAGRAM?). One of the jobs of those who create image sensors is to limit digital images to what humans can see (otherwise pictures wouldn't reflect what we see with our eyes). Evidently most commercial CMOS image sensors pick up infrared light quite well, which needs to be filtered out. In order to do this, almost all commercial webcams, cell phones, or cameras include infrared light filters, which can come in many different forms. 
Since the goal of this project is to pick up infrared light flashes, this is obviously a problem. In order to fix this, I took a brand new Microsoft Lifecam HD-3000, and proceeded to crack it open so I could identify and remove the IR filter. 

(Insert some images here with captions illustrating the disassembly process)

In addition, in order to reduce the amount of signal processing needed downstream, I added a visible light filter to the webcam. Now *only* IR light would be picked up, which at night meant pretty much only the IR LED on my sensor would cause visible flashes. 

(Insert quick video sample of flashes)

### Video processing with OpenCV
