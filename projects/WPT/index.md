---
layout: default
title: PCB Trace-based Wireless Power Transfer
---

# PCB Trace-based Wireless Power Transfer

### Motivation
For whatever reason, I find Christmas to be a source of inspiration for personal projects. Since 2019 I had been toying with the idea of making a [persistence of vision](https://www.youtube.com/watch?v=JrcKJOdjQN8) Christmas tree, where LEDs along the edge of a tree-shaped PCB would carve out a 3D tree shape that could also display text or animations. I have the unfortunate tendency to overcomplicate personal projects, and thinking through the project requirements I decided that using a either a slip ring or having separate power supplies for the spinning and stationary portions of the device would be inelegant solutions, despite this being how basically every hobbyist POV display is implemented. Instead I chose to use wireless power transfer. However, in my continuing effort to make the project even more complicated, I decided I didn't want to use an off-the-shelf Qi charging system, and instead wanted to roll my own wireless power transfer system, using coils constructed from PCB traces. 

Ultimately, I realized that this was a terrible idea ([hover for spoilers](./ "PCB trace DCR is much higher than Qi coils")), but I had a lot of fun along the way, and did in fact manage to get the wireless power link up and running.

ADD VIDEO HERE

### The Load
Before designing the actual power transfer portion, I first had to decide what the system would actually be powering. Since the eventual goal was to build a persistence of vision display, this would essentially serve as a platform board with LEDs, shift registers to drive them, a hall sensor, and a microcontroller to manage the system. For fun, I also included a temperature sensor (so this could also serve as a room thermometer) and a current sense amplifier, for power diagnostics.
I wanted to start building something that could display text, so I settled on a 5x8 array of LEDs. Based on my expected max currents, I came up with a very generous power budget of around 400mA.
For the microcontroller, I selected the STM32G031C6 - I happened to have some on hand, and this part met all of my requirements (IO count, I2C, ADC).

### The Wireless Link
To keep life simple, I decided to keep my coil turns ratio as 1:1. Additionally, from reading a few papers on the topic, I decided to use series-series resonant matching on both the transmit and receive sides, as this would have the least variance in response to system parameters. My primary concern during the design phase was that low efficiency would make it difficult for me to power the load, so I opted for a full-bridge driver (class D) on the TX side, to double my drive voltage. On the RX side, I included a full-bridge rectifier feeding directly into a wide-input (4.4-50V) buck converter down to 3.3V (the level required by the STM32). 

Calculating the expected inductance of the PCB coils was a very imprecise endeavor. I originally used some online calculators to come up with an initial target of 13uH, but the real value I ended up with was around 52uH (with a DCR of 3 ohms). 

### The Coil Driver

Doing a few rounds of LTspice simulations helped uncover a few critical issues before I actually committed to the design:
1. Shoot-through was a major risk that could easily blow up my FETs. To mitigate this I used gate drivers, and also planned for a drive waveform with deadtime inserted between the negative and positive phases of the switching cycle. This was a major deciding factor in my decision for the coil driver to use a STM32F070F6 - the advanced timer already had the ability to generate complementary PWM outputs with inserted deadtime.
2. In my simulations I was getting huge current spikes (110A at initial turn-on, and 4A recurring) in the low-side FETs. I eventually tracked this down to excessively fast charging of the bootstrap caps, where adding a 10 ohm resistor reduced that initial spike to 1.1A and the recurring spikes to 600mA.
3. The resonant tank was showing voltages of >70V, so I opted for polypropylene WIMA film caps.

To round out the design, I used the same 3.3V output buck as the receiver side to generate the 3.3V needed for the microcontroller. The system is powered from 12V, to give a 24V swing across the resonant tank, and also because I happened to have some 12V power supplies from past projects.

### The Good

### The Bad

### The Ugly

### The End
