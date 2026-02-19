# Colour CRT Monitor for a Retro Luggable Computer

I'm in the process of assembling a luggable Apple ][ clone system.  This is the colour monitor part of the project.

This monitor started out as a SKYTEK brand SCT-1024 portable colour television that I picked up from an auction site.  The video baseband and CRT driver section is built around a Panasonic 150CHB22 picture tube driven by a Toshiba TA7698AP video-chroma-deflection system all-in-one chip.  I think those tubes were quite common in small televisions of this type, and the all-in-one chip is easy to find, so this project might be useful for others looking for a way to make a small DIY CRT colour monitor from salvaged parts.

## Notes

There are no documents for this specific make and model online that I can find.  The datasheet for the TA7698AP chip is available, which includes a reference circuit for an NTSC application.  That chip was used in at least one colour television service technician training kit, so there's a lot of helpful tutorial-like material floating around online about the operation and maintenance of circuits based on it.  Researching small portable televisions I learned that SKYTEK was probably one of a pantheon of small Korean and Taiwanese companies that licensed or copied older Japanese designs and sold them into the discount market, and based on that I looked for cosmetically similar Japanese televisions and found the JVC CX-60, which appears to be physically identical.  Service manuals for the PAL/SECAM European version of the CX-60 are available online, but not NTSC.  Other than the colour processing circuits, the schematic matches almost exactly what's inside this television, even the component designators are identical or closely related, and so should provide a useful guide for adjusting the circuit.

Using the PAL/SECAM CX-60ME service manual and the TA7698AP datasheet as guides, I traced out the schematic for my set.  The two major differences from the CX-60ME are:
* The absence of the PAL/SECAM colour processing circuitry ... replaced with a tint control.
* The SKYTEK has a different flyback transformer, with fewer taps on the primary winding.  In particular, the CX-60 derives a +17 V DC supply for the vertical deflection circuit from one of its transformer's primary's taps, whereas the SKYTEK doesn't have that and uses its main regulated 11 V DC supply to power the vertical deflection.  The substantially lower voltage leads to significantly different component values in that part of the circuit.

The Panasonic TH6-X3V uses the same picture tube, but a different all-in-one chip.  Other TVs that I believe are similar, that I found in my search for a schematic, include:
* Jericho color TV J-210
* Jaxon CT-105
* Hitachi C6-GL3
* National TH6-X300
* National TH6-X7

## Power Supply

The TV was powered by an external unregulated DC power supply labelled 12 V, but the TV was also intended to be powered from an automotive cigarette lighter, so it was basically getting fed about 14 V, unregulated.  Internally, that feeds a linear 11.0 V DC regulator from which everything is powered.  That regulator is precise:  it has a temperature compensated zener reference and a voltage adjust pot.  So the circuit needs a clean, precise, regulated 11.0 V supply.  It would be very convenient if the circuit could run on a (clean, regulated) 12 V supply because that's one of the power supplies the computer already has.

Most of the circuit is actually powered by a 10 V rail which is derived from the 11 V rail using a simple series drop resistor.  Adjusting that resistor is probably all that's needed to accommodate a higher 12 V supply.  The real problem is the horizontal and vertical deflection circuits.  I don't know enough about these circuits to understand how the supply voltage affects the component values needed to get the amplitude and frequency of the oscillations in spec.  However, given the dramatic changes in resistor values for the transistor biasing in the vertical deflection circuit compared to the CX-60, where that circuit runs on 17 V, I think changing the supply voltage to these circuits needs to be done with care.  For now I'm expecting to have to include an 11.0 V regulator circuit.

## Status

- [X] First pass through the circuit, entering it into kicad.
  - CRT socket board not included.  Will reuse as-is.
  - 11 V regulator circuit not included.  Considering alternatives.
- [ ] Remove components from PCB, confirm values out-of-circuit.
- [ ] Select new replacement parts.
- [ ] Assign footprints.
- [ ] Layout PCB.
