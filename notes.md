# Notes

There are no documents for this specific make and model online that I can find.  The data sheet for the TA7698AP chip is available, which includes a reference circuit for an NTSC application.  That chip was used in at least one colour television service technician training kit, so there's a lot of helpful tutorial-like material floating around online about the operation and maintenance of circuits based on it.  Researching small portable televisions I learned that SKYTEK was probably one of a pantheon of small Korean and Taiwanese companies that licensed or copied older Japanese designs and sold them into the discount market, and based on that I looked for cosmetically similar Japanese televisions and found the JVC CX-60, which appears to be physically identical.  Service manuals for the PAL/SECAM European version of the CX-60 are available online, but not NTSC.  Other than the colour processing circuits, the schematic matches almost exactly what's inside this television, even the component designators are identical or closely related, and so should provide a useful guide for adjusting the circuit.

Using the PAL/SECAM CX-60ME service manual and the TA7698AP data sheet as guides, I traced out the schematic for my set.  The two major differences from the CX-60ME are:
* The absence of the PAL/SECAM colour processing circuitry ... replaced with a tint control.
* The SKYTEK has a different fly-back transformer, with fewer taps on the primary winding.  In particular, the CX-60 derives a +17 V DC supply for the vertical deflection circuit from one of its transformer's primary's taps, whereas the SKYTEK doesn't have that and uses its main regulated 11 V DC supply to power the vertical deflection.  The substantially lower voltage leads to significantly different component values in that part of the circuit.

## Power Supply

The TV's DC barrel jack is labelled "12 V".  It was powered by an external unregulated DC power supply labelled an oddly precise 14.7 V at 1.3 A.  The TV was also intended to be powered from an automotive cigarette lighter, so it was basically getting fed about 14 V, unregulated.  Internally, that feeds a linear 11.0 V DC regulator from which everything is powered.  That regulator is precise:  it has a temperature compensated zener reference and a voltage adjust pot.  So the circuit needs a clean, precise, regulated 11.0 V supply.  It would be very convenient if the circuit could run on a (clean, regulated) 12 V supply because that's one of the power supplies the computer already has.

Most of the circuit is actually powered by a 10-ish V rail which is derived from the 11 V rail using a simple series drop resistor.  Adjusting that resistor is probably all that's needed to accommodate a higher 12 V supply.  The real problem is the horizontal and vertical deflection circuits.  I don't know enough about these circuits to understand how the supply voltage affects the component values needed to get the amplitude and frequency of the oscillations in spec.  However, given the dramatic changes in resistor values for the transistor biasing in the vertical deflection circuit compared to the CX-60, where that circuit runs on 17 V, I think changing the supply voltage to these circuits needs to be done with care.  For now I'm expecting to have to include an 11.0 V regulator circuit.

Oddly, the TA7698AP data sheet recommends a supply voltage of 12 V, and says the minimum operating voltage is 10.8 V, which is *above* the voltage indicated on the VCC pin in the CX-60 schematic (10.6 V).

## Diodes

All diodes specified to be 1SS133 in the CX60 schematic are, here, parts marked "48T" around their barrel, or some cyclic permutation thereof (don't know where the start and end of the sequence are).  I believe they are 1N4148 equivalent.

The diode (D405) used to rectify the 90 V supply for the CRT socket board has burned the PCB.  That's the only place on the PCB with evidence of heat, the only indication of a component that needed to handle more power than it was rated for.  I don't know what the diode is, I can't make heads or tails of the markings on it ("K4K4"), but I want to choose something else.  The diode seems healthy, so my plan is to re-use it, then with it in circuit measure the current and voltage it needs to handle, and replace it with something beefier that doesn't get as hot.  The CX60 used an ISR124-400A which is a 400 V 1 A high-speed rectifier diode.

In the CX60 schematic the horizontal pulse signal providing a phase sense feedback from the horizontal fly-back circuit feeds into four portions of the circuit:
1. a circuit to derive a horizontal blanking signal that gets mixed into the luminance;
2. the circuit tuning the horizontal oscillator;
3. the TA7698AP's h pulse in / gate pulse out pin;
4. the PAL/SECAM colour processor chip.

In the CX60, the 3 and 4 circuits share the same initial portions, including a zener diode (D303) to clamp the positive-going pulse amplitude.  A very similar but completely independent circuit is used for 1, which also includes a zener diode (D502) but where that diode creates a DC bias voltage and is not used to clamp the pulse amplitude.  The TV I've got has a nearly identical circuit for 2, and does not have the PAL/SECAM colour processor, and it combines 1 and 3 into a single circuit.  That circuit has basically the same parts as used in 3 above, but instead of a zener diode to clamp the positive-going voltage there is simply another "48T" diode (D402), identical to all the rest on the board.  This is more like the reference circuit in the data sheet, which shows a normal diode in this position.  I don't know what to make of this.  Did the person screw up and put the wrong component into this position, should it have been a zener diode?  Is the reference circuit in error?

I've decided to assume the reference circuit is correct, the diode does not need to clamp the positive-going voltage, and it can be the same as the other small signal diodes in the circuit.

## Inductors

All of the normal inductors I've managed to measure and document, but there are two that I don't know what to make of.

There is a tunable inductor thingy connected to the TA7698AP'S "burst cleaning" pin.  The package is marked KRFE204, and the circuit diagrams suggest it's a capacitor and variable inductor in parallel inside a single box.  The package I have has 5 pins, not including the tabs on the shield surrounding it, and only 2 of the 5 are used, the rest are all grounded.  I don't know what its parameters are or what it is exactly.  I'm reusing the one I pulled out of the TV, and will reconnect it the way it was hooked up.  Looking at the chip's block diagram, the various schematics, and considering what the pin description says about it, I /believe/ you're supposed to connect a DC-isolated LC band-reject filter to pin 10 that is tuned to the colour sub-carrier frequency (~3.58 MHz for NTSC) and that shunts everything away from its centre frequency to ground.  The pin description says the LC tank circuit's alignment can be used to shift the phase of the chroma signal w.r.t. the colour burst phase reference.  So ... I think you should look for a 3.58 MHz tunable LC circuit in a can which is to be deliberately mistuned slightly to provide a phase shift and tweak the colours.  I am expecting to adjust it by putting the tint control in the centre, and tweaking the inductor until the colours look right.  There are instructions in the CX60ME manual for adjusting this component (which it calls a transformer), but I don't yet know if they work for this circuit because of the differences in NTSC chroma processing.

There is also a transformer used to drive the horizontal output transistor.  Televisions seem to always include such a transformer, I don't really understand why, and so I don't know what this thing's properties are supposed to be.  I'm reusing the one I pulled out of the TV and connecting it up the way I found it.  Measuring this transformer:
 - grounded pins 2 and 4 (signal generator and oscilloscope chassis);
 - placed 15 kHz sine wave into pin 1, measured it to be 1 V peak-to-peak using 1 MOhm oscilloscope probe in parallel across winding;
 - simultaneously observed 250 mV peak-to-peak sine wave in phase on pin 3 with a second 1 MOhm oscilloscope probe across the winding;
 - with the ground lead for the probe on the secondary disconnected the amplitude increased to 1 V peak-to-peak.
 - the primary is measured to be 11 mH at 10 kHz with the secondary open, and 0.93 mH with the secondary shorted.

The two ferrite beads, L401 and L409, are 0.12 uH at 100 kHz.

## Resistors

- All variable resistors measured consistently 20% below their marked value.
- All blue resistors had drifted upwards, all were consistently measured 10% above their marked value.

## Transistors

### 2SC200

Can't find a manufacturer's datasheet for the 2SC200 transistor, Q401, used to drive the horizontal output transformer.  Web sites that claim to have data for this part contradict each other.  What I have found:
 - A contemporary transistor parameter table lists it as a Fujitsu part having RF conversion mixer, oscillator and power amplifier applications, states the collector-base breakdown voltage is 40 V, the max collector current is 300 mA, the max dissipated power is 600 mW, max junction temperature is 175 C, the transition frequency is 350 MHz, and the current gain is 60.
 - A contemporary cross-reference table says the 2SC200 can be replaced with:  2SC1175, 2SC594, 2SC138, 2SC139, 2SC696, 2SC741
 - The CX60 uses a 2SD639-Q or -R for its Q501, the equivalent in its circuit.  Those have:  collector-base breakdown voltage of 60 V, collector-emitter breakdown voltage of 50 V, max collector current 500 mA, max dissipated power 600 mW, max temperature 150 C, transition frequency of 200 MHz, and a current gain between 85 and 240.
 - One web site claims the transistor has a collector emitter breakdown voltage of 45 V, a max collector current of 0.5 A, dissipates a maximum of 0.625 W, has a transition frequency of 300 MHz, and that the Y rating will have a current gain between 120 and 240.
 - That site claims it can be replaced with:  2SC2274, 2SC2274K, 2SC2277, 2SC2655, 2SC3243, 2SC3328, 2SC3916, 2SC3917, 2SC3918, 2SC3919, 2SC3920, 2SC3921, 2SC3922, 2SC3923, 2SD438, 2SD667, 2SD863, BC635, KSC1008C, KSC2331, KTC200, KTC3209 or KTD863.
 - Another web site claims it has a collector base breakdown voltage of 40 V, a max collector current of 0.3 A, dissipates a maximum of 0.65 W, has a transition frequency of 175 MHz, and a current gain of 60 (regardless of rating).
 - 2N5551, 2SCR514R might be suitable modern replacements

## Delay Line

I have no idea what this is.  "Sangshin DL601T" is written on it, but I can't find any information on that.  The CX60 schematic isn't helpful because that's for a PAL/SECAM television which would need a different component here.  The TDA7698AP reference circuit shows a TRF2033 or TRP2033 (bad scan) used in this part of the circuit, and I can't find any information about that on the internet.  The schematic calls it a 3.58 MHz trap, and the block diagram suggests it is an LC band reject filter combined with a delay line.

I tried injecting a 100 mV sine wave into pin 1, grounding pin 2, looking at the output on pin 3, and sweeping the frequency between 1 MHz and 5 MHz.  It definitely seems to be a filter, but my quickie hack wasn't enough to get a sense of what kind it is.  However, if it's also a delay line that's hard to tell.  At low frequencies, below 100 kHz, there's no obvious phase difference, no time delay between the input and output signals.  Increasing the frequency reveals a frequency dependent phase shift but not one that corresponds to a fixed time delay.  Injecting, instead, a square wave produces long ringing tails on each transition.  In the circuit, it has a 10 V DC bias across it, between both its input and output and the ground pin.  I wonder if that's needed to make it work.  There might be active circuitry inside it.

## Other Similar Televisions

The Panasonic TH6-X3V uses the same picture tube, but a different all-in-one chip.  Other TVs that I believe are similar, that I found in my search for a schematic, include:
* Jericho color TV J-210
* Jaxon CT-105
* Hitachi C6-GL3
* National TH6-X300
* National TH6-X7
