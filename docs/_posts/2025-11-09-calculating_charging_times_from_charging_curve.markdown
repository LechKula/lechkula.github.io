---
layout: post
title:  "Charging times from a charging curve"
date:   2025-11-09 15:47:00 +0100
categories: jekyll update
---
The following post is based on part of my Bachelor's thesis, available at [Tillgänglighet och pris för snabbladdning av elektriska personbilar](http://hdl.handle.net/20.500.12380/307720 "Tillgänglighet och pris för snabbladdning av elektriska personbilar") (in Swedish). The thesis was done in collaboration with other students and no part of the thesis was done independently, we all worked and contributed to it. So a shoutout to my group mates! 

The charging time of electric vehicles (EVs) depends on many factors. One fundamental factor is the power supplied to the EV. How much power is supplied depends on the power supplied from the charger, which depending on model can range from a few kilowatts until a few hundred kilowatts. It is not only the charger that depends the power supplied, but also the battery in the electric vehicle. Not only does this vary between different car models, but also depends on the state of charge (SOC) of the battery, or the percentage of the battery charged. The state of charge is given by 

$$
SOC = \frac{E_{battery}}{E_{capacity}}
$$ 

with $E_{battery}$ being the energy in the battery and $E_{capacity}$ the total capacity of the battery. This number is usually presented as an percentage. 

A simple explanation for how the state of charge impacts charging power is that if the state of charge is high, there are many negative electrons in the battery and this creates a repulsive force to the electrons from the charger. Reality is not this simple though, for instance the power supplied when the battery has a very low SOC (close to 0%) is also lower, due to safety concerns. The maximum power supplied will generally be at around 10% -- 30% SOC. This can be seen in the following figure, a charging curve for a Tesla Model 3 with a V2 Supercharger with a maximum power of 150 kW.
![charging curve 150 kW](/assets/charging_curve_150_kw.png)
From the curve, the behaviour I discussed can be seen. The highest power supplied is a bit before 10%, with it being close to 150 kW during the range 10% -- 30%.

## Calculating charging time
With a charging curve as the one above, calculating the charging time is possible. The energy delivered is the power times the time charged, meaning that if $E_{charge}$ is the energy to be supplied then we are looking for the upper bound $T$ in the following integral

$$
E_{charge} = \int_0^T P(SOC) \ dt 
$$ 

with $P(SOC)$ being the power delivered depending on the batteries current state of charge. This will depend on the energy supplied during charging, because the energy in the battery $E_{battery}$ will increase by this and in turn increase the SOC as given by the fraction presented above. Therefore we have an integral equation where the unknown is $T$. To simplify this integral equation, the integral can be treated as a sum and the power supplied $P(SOC)$ to be piecewise constant for small time steps $\Delta t$ (this is the Riemann sum)

$$
E_{charge} = \int_0^T P(SOC) \ dt \approx \sum_{k=0}^m P(SOC(t_k)) \cdot \Delta t
$$ 

with $t_k$ being the discrete time, given by $t_k = k\cdot \Delta t$. We can calculate a step in the sum and check if the energy supplied at this timestep is greater or equal to the requested $E$. If no, then we calculate another step. If yes we are done with $T \approx m\cdot \Delta t$. From the theory of Riemann sums, if $\Delta t \rightarrow 0,\ m \rightarrow \infty$ then our approximation will converge to the true value. For practical applications this is impossible, but also unnecessary because the charging curve does not change too rapidly as seen in the figure above. Coding this is as simple as a while loop

{% highlight python %}
E_capacity = 75 # battery capacity in kWh 
E_battery = E_capacity*0 # initial charge in battery
E_target = E_capacity*1, # E_target =  E_charge + initial energy in battery
delta_t = 1/360000 # time step in hours, this corresponds to 1 s
t_k = 0
while E_battery < E_target:
	SOC = 100*E_battery/E_capacity # Calculate SOC, converting to %
	E_battery += P(SOC) * delta_t 
	t_k += delta_t
T = t_k
{% endhighlight %} 

The above code snippet assumes we have a function that gives the power $P$ when fed with a SOC. You might have such a function, supplied from the manufacturer or developed by yourself. If not, then a charging curve can be derived from data. 

## Interpolating a charging curve
Data on charging curves for different electric vehicles, is to my knowledge, not freely available. Therefore, methods to find a charging curve are of great interest. Using data is one approach. There are instances of EV users testing the performance of their vehicle and uploading this data. One such test was done by Tom Moloughney at InsideEVs for a Tesla Model 3 Long Range from 2021, [Tesla Supercharger Showdown: Is V3 Really Much Faster Than V2?](https://insideevs.com/reviews/516438/tesla-supercharger-comparison-review/ "Tesla Supercharger Showdown: Is V3 Really Much Faster Than V2?"), with power given at the corresponding SOC for increments of 3%. Data for the 150 kW V2 charger can be seen in the following figure.
![charging curve data](/assets/charging_curve_data.png)
Looking up the power corresponding to a SOC from the data points could not fulfill the requirements for the P(SOC) function, since data is only available at every 3% SOC. An assumption of the data being piecewise constant could be made, but even better is to interpolate the data. Multiple interpolation methods exist, but cubic spline interpolation has many good properties. A spline is a piecewise polynomial function. Piecewise polynomials are e.g. used in the continuous Galerkin method for solving initial value problems (M. Asadzadeh An Introduction to the Finite Element Method for Differential Equations). In the following SOC and their corresponding power values are given as arrays, and then the cubic spline interpolation from SciPy is used. 

{% highlight python %}
import numpy as np
from scipy.interpolate import CubicSpline
# Data for V2 Supercharger (from https://insideevs.com/reviews/516438/tesla-supercharger-comparison-review/)
SOC = np.array([0,1,3,6,9,12,15,18,21,24,27,30,33,36,39,42,45,51,57,66,69,78,84,90,93, 96,99,100])
Power_V2 = np.array([90,95,105,150,150,150,150,150,150,150,150,140,135,120,100,90,90,90,90,90,85,60,50,43,32,25,15,0.1])
# Interpolation
P = CubicSpline(SOC,Power_V2) # The requested power function

{% endhighlight %}

Here P will be a piecewise polynomial class in SciPy, which we can use as the function P in the while-loop above. Finally we can test our interpolation to check its fit to the data. 
![charging curve interpolation fit](/assets/charging_curve_interpolation.png)

The full code snippet for both calculation and interpolation can be found below, with charging from 0 % to 100 %
{% highlight python %}
import numpy as np
from scipy.interpolate import CubicSpline

# V2
SOC = np.array([0,1,3,6,9,12,15,18,21,24,27,30,33,36,39,42,45,51,57,66,69,78,84,90,93, 96,99,100])
Power_V2 = np.array([90,95,105,150,150,150,150,150,150,150,150,140,135,120,100,90,90,90,90,90,85,60,50,43,32,25,15,0.1])
# Interpolation
P = CubicSpline(SOC,Power_V2) # The requested power function

E_capacity = 75 # battery capacity in kWh 
E_battery = E_capacity*0 # initial charge in battery
E_target = E_capacity*1, # E_target =  E_charge + initial energy in battery
delta_t = 1/360000 # time step in hours, this corresponds to 1 s
t_k = 0
while E_battery < E_target:
	SOC = 100*E_battery/E_capacity # Calculate SOC, converting to %
	E_battery += P(SOC) * delta_t 
	t_k += delta_t
T = t_k

print(T*60) # print time in minutes, for our case 71.5 min

{% endhighlight %}

We see that it takes roughly 1 hour and 11 minutes to charge a Tesla Model 3 Long Range. This is very close to the 1 hour and 10 minutes reported by Tom Moloughney ([Tesla Supercharger Showdown: Is V3 Really Much Faster Than V2?](https://insideevs.com/reviews/516438/tesla-supercharger-comparison-review/ "Tesla Supercharger Showdown: Is V3 Really Much Faster Than V2?")).
