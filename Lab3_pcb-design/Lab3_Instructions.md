# Lab 3: PCB Design

## Goals
- Reinforce how to do circuit schematics and read data sheets
- How to download and import symbols and footprints
- Learn how to do planes and implement them in KiCAD
- Modifying Edge Cuts
- PCB layout and routing

## Deliverables
Refer to the canvas page [PCB Design Lab](https://canvas.uw.edu/courses/1860902/assignments/10960950) for the deliverables for this lab.

## Lab Prerequisites

- KiCAD installed on your computer

## Useful Resource

- [Youtube KiCAD Tutorial Series](https://www.youtube.com/watch?v=vLnu21fS22s&list=PLUOaI24LpvQPls1Ru_qECJrENwzD7XImd)
- [Digikey Youtube Series](https://www.youtube.com/watch?v=0WCi1rhueH4&list=PLEBQazB0HUyQ5YJSdCBb79orXaR3Uk5vm)
- [Shawn Hymel Youtube Series](https://www.youtube.com/watch?v=vaCVh2SAZY4&list=PL3bNyZYHcRSUhUXUt51W6nKvxx2ORvUQB)
- [How to add a Ground Plane](https://www.youtube.com/watch?v=DNTgrTukltw)
- [David Jones Article](https://www.scs.stanford.edu/~zyedidia/docs/pcb/pcb_tutorial.pdf)
- [Importing Symbols and Footprints](https://forum.kicad.info/t/how-to-get-a-downloaded-symbol-footprint-or-full-library-into-kicad-version-5/19485)
- [SnapEDA footprint and symbol library](https://www.snapeda.com/)
- [Ultra Librarian footprint and symbol library](https://www.ultralibrarian.com/)



## Instructions:
For this lab, you will design your own [breakout board](https://soldered.com/learn/breakout-boards-what-are-they-and-why-you-should-use-them/) for a sensor assigned to you. Everyone in this class has used a breakout board; the accelerometer you used in your TECHIN512 final project was on a breakout board. You will need to create a circuit schematic and complete the PCB layout for your breakout board. Each student will be assigned an i<sup>2</sup>c sensor, a voltage regulator, and a footprint size (ex: 0805, 0603, 0402) for their resistors, capacitors, LEDs, etc. Each board will have three parts: the sensor, the voltage regulator, and the led. Each of those parts has their own resistors or capacitors that you will need to determine for them to work properly. 



Some i<sup>2</sup>c sensors have the ability to do both i<sup>2</sup>c and SPI, you only need to do connections for i<sup>2</sup>c. Your board should have four header connections for Voltage, Ground, SDA, and SCL. If you cannot find the footprint/symbol for your components on [Digikey](https://www.digikey.com/), we recommend checking SnapEDA or UltraLibrarian. There are additional constraints for your board design listed below.

### Constraints
- Your board must have a power LED. When your board receives power, the LED turns on
- Your board must use only surface mount (SMD) components
- Your board must have a ground plane on the same side you are placing your components
- The edgecuts (outline of the board) should not have any sharp corners


## Example

Here is an example of a breakout board using an MLX90393 Magnetometer under the same contraints.


Schematic:


![Board Schematic](assets/Schematic.svg)



PCB Layout:


![PCB Layout](assets/PCBLayout.svg)


Manufactured Board:


![Board](assets/Manufactured_Board.svg)


