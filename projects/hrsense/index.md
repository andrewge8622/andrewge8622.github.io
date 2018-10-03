---
layout: default
title: Heartbeat Sensor
---

# Heartbeat Sensor

### Motivation

During my sophomore year, I spent a night in the hospital for unimportant reasons (*cough exhaustion cough*). During my stay, they had me hooked up to a pulse-ox, which also gave a readout for pulse. This became relevant, because apparently there was a built-in alarm that would go off if a patient's pulse dipped below 45. Mine did this whenever I started to fall asleep, which was not helpful for the whole exhaustion thing.

45 is quite low for a heart rate, especially considering I'm not an athlete by any means. I've been wondering since then what my heart rate is like at night, and whether the fact that my HR was so low that night only due to dehydration/exhaustion/starvation/what have you.

So of course I built a sensor to track it for me! Because buying a fitbit would be too easy.

### Overview

The sensor uses a small piezo to detect heart rate when strapped to my finger. It uses two custom PCBs, worn around the wrist. One is used just for batteries. The other collects the voltage from the piezo, does some basic amplification/thresholding, and triggers a high power infrared LED when a pulse is detected. This infrared flash isn't visible to the human eye, so it won't bother me while I'm trying to sleep, but the signal can be picked up by a modified webcam. Storing 6+ hours of footage and analyzing it manually sounds like an actual punishment, so instead I wrote a basic C++ program using OpenCV to record light intensity in each frame, and then used a combination of C++ and MATLAB to extract pulses from the light intensity data, and plot it across the time I spent asleep.

### Piezoelectric effect ELI5

### Circuitry

### Modding a webcam to see IR
Humans can only see a very limited portion of the EM spectrum (INSERT DIAGRAM?). One of the jobs of those who create image sensors is to limit digital images to what humans can see (otherwise pictures wouldn't reflect what we see with our eyes). Evidently most commercial CMOS image sensors pick up infrared light quite well, which needs to be filtered out. In order to do this, almost all commercial webcams, cell phones, or cameras include infrared light filters, which can come in many different forms. 
Since the goal of this project is to pick up infrared light flashes, this is obviously a problem. In order to fix this, I took a brand new Microsoft Lifecam HD-3000, and proceeded to crack it open so I could identify and remove the IR filter. 

(Insert some images here with captions illustrating the disassembly process)

In addition, in order to reduce the amount of signal processing needed downstream, I added a visible light filter to the webcam. Now *only* IR light would be picked up, which at night meant pretty much only the IR LED on my sensor would cause visible flashes. 

(Insert quick video sample of flashes)

### Video processing with OpenCV
