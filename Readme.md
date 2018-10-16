

<i>Note: As a hobbyist learning electronics and assoicated technologies, I like to document my projects to assist my learning and for later reference.  I learn heavily from others so there will likely be nothing here that is particularly novel - but if you stumble across these notes, beginners might find these notes constructive. </i>

# Low Duty Cycle 555 Oscillator

## Purpose

Low duty cyle (2.5%) oscillaor based on a 555 chip designed to act as a minimal power draw or "dummy load" in order to defeat the auto-off circuitry of a commercially available power bank so that it can be used to power other electronic projects.


## Backgound / Context

I own a Jackery Giant+ Powerbank that I want to use to power 5V projects, through a USB-A 2.1A connection.  As long as there is an ongoing load drawing on the battery bank, it works great!  However, if the load is low or intermittant, the battery bank is designed to auto-shut off to preserve the battery life after a set period of no load.  For example, once a device is fully charged and the load drops off to the battery bank, the auto-off feature will sense this and shut the battery bank down.

As a result, what may be a useful consumer battery bank feature for recharging devices ends becomming a problem if the battery bank is going to be used to power an electornics project.  
 
If I design a project that only draws power itermittantly, the Jackery will auto-shut off. For example, if I design a gadget that sleeps between activities to minimise battery draw, the power bank will shut down during the first sleep period.    


### Solutions by Others
Others have overcome this issue in two ways:  

1. Change out current sensing resistor in battery bank; or
2. Add a dummy load on the battery bank.

#### 1. Changing the current sense resistor

Some have chosen to hack the battery bank circuitry to defeat the auto-off feature.  Anurag Chugh has a nice blog at [http://www.electronicsfaq.com/2014/10/replacing-current-sense-resistor-in.html]() where he shows how he replaced the R100 (0.1 ohm) SMD current sense resistor with a 10 ohm through-hole resistor; an increase of the current sense of 100 times.

I chose not to go with this solution as 1) this is aimed at low power projects where your project current is below the current sense threshold while my projects will have periods of extremely low draw as they sleep; and 2) I did not wish to break open the power bank and risk damaging it to get to the circuitry.

#### 2. Dummy Load

Paul Stoffregen has a nice blog post where he used a microcontroller to work out the minimum load duration required to keep a specific power bank alive.  

[http://www.dorkbotpdx.org/blog/paul/battery_pack_load]()   

This approach also caught the eye of Hackaday - written up here: [http://hackaday.com/2013/11/08/tricking-a-usb-power-supply/]()

Paul found out that for his specific battery bank, he only needed a ~200mA pulse on a 1.6% duty cycle .  The advantage of this over coninuous loads like resistors is the total draw is only a negligible 3.5 mA given the oscillator is off most of the time with only short 20-30 msec pulses.

Rather than stay with microcontroller "overkill", Paul even created a simple analog circuit based on a pair of coupled NPN / PNP transistors and an RC (resistor - capacitor) timer.  This simple circuit was constructed on a tiny PCB mounted onto a USB male plug.  Plugged in, this cirucuit cretes a minimal pulsed load to keep the battery bank powered up yet has a very low overall draw.

Originally, I intended to recreate this circuit but through my research I couldn't find much reference to the circuit as an oscillator. In the Hackaday article comments, one reader noted that this circuit would not simulate in SPICE while another explained that this was because the circuit was not robust and subject to latchup (see Hackaday article comments for more detail).  As such, I decided to seek out an alternative, more reliable circuit design.



## Low Duty Cycle Oscillator

I started by searching through online sources and within electronics books for a suitable candidate circuit. Options included two-NPN or 555 based multibrators.  
 
Surprisingly, I had a bit of trouble findng a circuit design for a ultra-low duty cycle oscillator.  

I thought a 555-based design would be ideal as components are cheap and the chip has been around for so long that there are a huge number of published designs.  In the 555 world, low duty cycle circuits are often called timers however, they are typically designed for monostable pulses.  I found suitable purpose-built timer integrated chips but they were quite costly.

Eventually, I came across a low duty cycle osciallator circuit based on a 555 timer chip displayed within a neat animated Java circuit simulator:  [http://www.falstad.com/circuit/e-555lowduty.html]().



### Design - LTSpice

First, I decided to investigate the above circuit further using LTSpice where I could play with the component values to determine if I could achieve my design goals of   

1. duty cycle < 3%. 
2. 20-30 msec on (high) pulse
3. current (high) ~200mA
4. simple and low cost



After playing with the component values, the LTSpice model gave me 


<u> Oscillator Modelling Results </u>

* Analog circuit; based on 555 chip
* 2.6% duty cycle <br>
* 597 mHz (10^-3 Hz)<br>
* LTSpice: 42.9ms on (high) per 1.67 sec interval
* 193mA pulse current; generating an acceptably low 5mA "average" current draw

![](https://github.com/cwgstreet/Low-duty-cycle-555-oscillator/blob/master/Pics/LTSpice%20Composite.jpeg)


### Schematic & PCB

Next, I drew up the schematic and PCB in KiCad.  For flexibilty and to keep costs low, I prefer etch my own homebrewed PCBs. My approach was to design the board using a combinatiuon of both surface mount and through-hole components that I already had around.  An added advantage of this approach was it made it easier to keep the board small as components could be placed on both sides yet the board remain a simple DIY etch-at-home single sided board.


![Schematic KiCad Eschema](https://github.com/cwgstreet/Low-duty-cycle-555-oscillator/blob/master/Pics/Kicad-schematic.jpeg)

![PCB with and without filled ground plane showing - KiCad PCBNew](https://github.com/cwgstreet/Low-duty-cycle-555-oscillator/blob/master/Pics/PCB%20-%20composite.jpeg)


![Both sides of PCB in 3D - KiCad 3D](https://github.com/cwgstreet/Low-duty-cycle-555-oscillator/blob/master/Pics/PCB%20-%203D%20composite.jpeg)


### Testing & Prototyping

#### Validation
To validate the LTSpice Model with real components, I laid out the circuit on a breadboard using all through-hole (critical - no switch, etc) components and tested it with the Jackery Giant+ PowerBank to ensure it defeated the auto-off feature.  

![breadboard photograph](pic)  breadboard pic - to be added

Once the circuit was laid out on the breadboard, I used a Rigol 1054Z Oscilloscope to compare the actual oscillator response with the idealised LTSpice simulated response.  


#### Prototyping

I quickly created a homebrew PCB using the toner transfer and hydrogen peroxide - HCl etching.



