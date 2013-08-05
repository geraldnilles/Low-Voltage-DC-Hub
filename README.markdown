Low Voltage DC Hub
==================


# Goal
Create a very efficient AC/DC converter to replace every wall-wart plug currently in my living room.

# Motivation
Today, every low power device comes with its own 5V or 12V AC/DC converter.  These converters are annoying for a few reasons.  First, they take up a lot of space.  Second, they each have their own quiecent current that is wasting power.  Third, they were all made as cheaply as possibly and are likely inefficient. 

By combining all of the 5 and 12V converters into a single, large converter, we can reduce the number of plugs used, and improve power efficiency.  

# Basic Design

## Input Fuse
I will probably want to add an input fuse for safey reasons.  1A would probably be enough to power everyting i need (120W)

## Input Filter
I noticed that all AC/DC converts have a transformer based filter on the input.  I think its for EMI?

## AC/DC
There will be a simple Diode rectifier to convert the 120V AC to 170V DC.  Nothing exciting here.

## PFC Boost
In general, Buck converters creates a lot of noise at its input.  By design, a buck's input current turns on and off very suddently at over the 100kHz.  This creates a very ugly looking power factor that could fuck up other electronics on the same line.

To get around this, we will add power factor correction.  In an ideal world, every electrical load would be purely resistive or "real".  This means that the input current for each load is proportional to the input voltage.  Power factory correction (PFC) is essentially a boost converter that tryes to keep input current steady.  Since the input stage of a boost converter is an inductor, it tries to keep a constant current naturally.  By monitoring the input voltage and adjusting the duty cycle accordingly, we can keep the load resistive as far as the grid is concerned.  

## Fly Back Converter
After the PFC, we will want to convert the several hundred volts to 12V and 5V.  A flyback converts is basically a buck converter that uses a transformer to keep the duty cycles normal.

### Transformer
The transformer will probably need to be custom wound.

First, we need to figure out how many windings we need for each rail.  We will have 2 output rails: a 5V and a 12V.

As a rule of thumb, we want the windings to work out so that a duty cycle of 50% would output the correct voltages.  As load changes, this duty cycle will change too, but 50% will be used as our target.

THe proportions of the windings will be 1:2.4:34.  In other words, if the 5V rail has n windings, the 12V rail will have 2.4n windings and the primanry rail will have 34n windings.  

The value of 'n' will depend on the size and permiability of the core we select.  If the core's permiability is low, we will likely have to use a lot of windings, but we cant use too many so that we saturate the core.  


### Control IC
The control IC for the Flyback could either be an MCU or an off-the-shelf flyback IC.

The benefit of the off-the-shelf parts is that its highly integrated.  They will likely ahve a gate-driver built in.  

### Jumpstart
The contols IC will need 5V or  12V of power to start working.  One way to do this is with some jump-start circuitry.  When the power is initialy applied, there will be an inrush of current.  This inrush will hopefully provide enough power to the control IC to start switching.    

We coudl also use a resistor divider to provide the starting power and switch over to the 12V line once its done.  

### Isolation
It may be a good idea to isolate the outputs from the inputs.  This would allow the output 5V to take on whatever ground it is attached to.  For example, say you power your router using this circuit.  Then you connect your router to your laptop via eithernet and connect your laptop to the grid via a charger.   In this case, if the output is isoalted, the eithernet GND will be pulled to match the laptop GND and there will be no low resistance path.  If you dont isolate, it is theoritically possible that the other outlet is not porperly grounded and a GND loop could be created that damaagess electronics. 

I am not sure how practical this is to implement since everyting should be connected to earh GND, but its worth investigating.  

   
