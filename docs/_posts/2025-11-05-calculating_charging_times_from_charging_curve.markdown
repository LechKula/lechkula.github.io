---
layout: post
title:  "Charging times from a charging curve"
date:   2025-11-05 16:11:00 +0100
categories: jekyll update
---
The charging time of electric vehicles (EVs) depends on many factors. One fundamental factor is the power supplied to the EV. How much power is supplied depends on the power supplied from the charger, which depending on model can range from a few kilowatts until a few hundred kilowatts. It is not only the charger that depends the power supplied, but also the battery in the electric vehicle. Not only does this vary between different car models, but also depends on the state of charge (SOC) of the battery, or the percentage of the battery charged. 

A simple explanation for this is that if the state of charge is high, there are many negative electrons in the battery and this creates a repulsive force to the electrons from the charger. Reality is not this simple though, for instance the power supplied when the battery has a very low SOC (close to 0%) is also lower, due to safety concerns. The maximum power supplied will then be at around 10% -- 30% SOC. This can be seen in the following figure, a charging curve for a Tesla Model 3 with a V2 Supercharger with a maximum power of 150 kW.
![charging curve 150 kW](/assets/charging_curve_150_kw.png)
From the curve, the behaviour I discussed can be seen. The highest power supplied is a bit before 10%, with it being close to 150 kW during the range 10% -- 30%.

With a charging curve as the one above, calculating the charging time is possible. 
```math
\displaystyle\sum_{k=3}^5 k^2=3^2 + 4^2 + 5^2 =50
```
