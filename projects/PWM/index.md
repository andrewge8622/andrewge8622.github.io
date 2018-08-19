---
layout: default
title: Analog PWM generator
permalink: /analog-pwm-gen/
---

# Analog PWM generator

### Motivation

Over the course of ELE302 (popularly known at Princeton as Car lab), I discovered that breathing solder smoke for any period of times made me prone to headaches (read: *not good*). My immediate solution was to buy a PC fan on [amazon](https://www.amazon.com/gp/product/B01J76IYL4/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1 "in case you want one"). After googling the PC fan PWM control spec, I plugged the fan into a function generator + power supply, and happily continued soldering.

Of course, a function generator isn't something that I'll always have on hand, and I was curious about how I could go about generating a control signal without lugging around bench equipment everywhere. The classic solution, it would seem, is to just use a 555 timer. However, the results aren't super accurate, and that's also just boring. Instead, I decided to make an adjustable PWM generator with 3 stages of ICs

# Square wave generator
