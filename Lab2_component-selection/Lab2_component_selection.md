# Lab 2: Component Selection and Surface Mount Soldering

**This lab has two sections: analytical and physical**

## Goals

- How to generate a bill of materials (BOM) in KiCAD
- Learn about voltage regulators
- How to read data sheets
- Understand surface mount (SMD) comonent configurations and sizes
- Soldering surface mount components
- Read voltage on a microcontroller

## Deliverables

Refer to the Canvas assignment [EE Fundamentals Lab](https://canvas.uw.edu/courses/1860902/assignments/10960940) for what you need to submit for this lab.

## Lab Prerequisites

Before this lab, make sure you have the following ready:

* KiCAD installation on your laptop for PCB design
* **USB-C cable**: Make sure you can connect ESP32 to your laptop

## Section 1: Analysis and Component Selection

Step 1: Make sure you have cloned the TECHIN514_W26 repo to your computer. Open KiCAD, click on File and select Open Project (Ctrl+O). Navigate to where you have the repo saved on your computer. Go into the Lab 2 folder and select the regulator_demo folder. Inside of there open the regulators KiCAD project file. If you are prompted with a message about the import select Open Anyway.

Step 2: Generating a BOM using KiCAD

1. Once you have imported the KiCAD prooject, double-click the _sch file as shown below:

   ![1704914601583](image/Lab2_component_selection/1704914601583.png)
2. Then click Tool -> Generate Legacy Bill of Materials...

   ![1704914658150](image/Lab2_component_selection/1704914658150.png)
3. Select *bom_csv_grouped_extra* on the left then click *Generate
   ![1704914833159](image/Lab2_component_selection/1704914833159.png)*
4. The file should appear under the same directory as the schematic file, showing on the screen
   ![1704914904565](image/Lab2_component_selection/1704914904565.png)
5. Open the CSV file, notice that R1 through R8 all have unknown values. You will calculate these values based on the equations in the datasheets. Further instructions on how to do this are in the next step:
   ![1704914966570](image/Lab2_component_selection/1704914966570.png)

Step 3: Datasheet reading, calculation, and component selection

* You'll choose the necessary resistors to adjust output voltage of your regulators and matching current-limiting resistors for your LEDs.
  * For the LM317 regulator (the one with 0805 components and the left most circuit on the PCB), determine the resistor values to make the output voltage (VOUT1) equal to 2V + 0.01 * the day of the month you were born. So if you were born on the 15th, you should set the voltage to 2.15V;
  * For the TPS79301 regulator (0603 components, middle circuit on the PCB), determine the resistor values to make the output voltage (VOUT2) equal to 0.1 + VOUT1. For example, if you were born on the 15th set it to 2.25V;
  * For the MIC5377 regulator (0402 components, right most circuit on the PCB), determine the resistor values so the output voltage (VOUT3) is equal to VOUT2.

Here's a brief instruction about how to read a datasheet, using the LM317 regulator as an example:

1. Check the Terminal Configuration and Functions to determine the pinout for the device.
2. Check the Speficiations (Specs), especially Absolute Maximum Ratings of the device. This will ensure you won't destroy the device with high voltage/current;
   ![1704915396628](image/Lab2_component_selection/1704915396628.png)
3. For regulators and most other types of components, there will be a section called *Application and Implementation* or *Typical Application Circuit*. This demonstrates how to assemble it with resistors and capacitors. For voltage regulators, there should be an equation about how the output voltage is set: **the selection of the resistors will affect the output voltage.**
   ![1704915523873](image/Lab2_component_selection/1704915523873.png)

   There could be other details on the datasheet that might help your component selection. Please check them out carefully.

**For circuits with LEDs and resistors in serial, make sure you select a suitable resistor that will make the current forward (If) in a suitable range.** (Hint: To determine a suitable resistor value find the difference between the output of the regulator and the forward voltage of the LED. Use Ohm's law with the LED's DC forward current found in the datasheet)

# ESP32 Voltage Measurement Exercise

The second part is physical, the goal here is to be familiar with the component packages you might be using in your designs.

## Instructions

Here is the PCB layout with the same device names as the schematics:
![]()![1704936122514](image/Lab2_component_selection/1704936122514.png)![]()![]()

### Connect to ESP32 and Read Voltage

- Connect the PCB output to an analog pin on the ESP32.
- Program the ESP32 to read the analog value from this pin.
- It's acceptable for some boards to be non-functional or read zero volts.

### Convert ADC Values to Voltage

- The ESP32 ADC provides values between 0 and 4095. Convert these to voltage readings.
- Use the formula: `Voltage = (ADC_Value / 4095.0) * 3.3` to convert the ADC readings to volts.
  - In practice, further calibration beyone the equation above is required if you want to read the analog accurately. Try calibrating your ESP32 to read the exact voltage output if you want.
- Display the voltage readings in the serial monitor.

## Additional Resources

- You can refer to this blog as reference to calculate voltage from the ADC [Random Nerd Tutorials - ESP32 ADC Analog Read](https://randomnerdtutorials.com/esp32-adc-analog-read-arduino-ide/).

## Safety Note

Always double-check connections and voltages with a multimeter before connecting your ESP32 to avoid potential damage.

## Frequently-asked questions (FAQ)

**Q: What should I do if I cannot find a **resistor** in the lab that exactly matches my calculation?**

A: This is a typical situation, and I believe you must have encountered it during your TECHIN512 class. You can choose one resistor in the lab whose resistance is the closest to your calculation. This, of course, will affect the voltage output and produce some error, but acceptable. However, be sure that you are filling in the spreadsheet with the **IDEAL, CALCULATED** resistance but not based on your selection, otherwise it would be difficult for us to check whether your calculation is correct or not.

**Q: My circuit is correctly connected, but when using the multimeter to measure the voltage, I cannot get the reading. What's wrong with my board?**

A:

* First, checking whether the ground (GND) pins are all connected. Using the left circuit as an example in the aboved figure, there are two pins on the PCB boards both representing the GND: the first one is the white circuit on the right of R2, and the second one is the white circuit above D1. Therefore, you shall use a wire to connect them.
* Also, your power supply negative and multimeter negative/ESP32 GND shall be connected to those pins as well.
* If they're still not working, probably your board is mis-printed and need detailed debugging. You can use a multimeter and switch it to the connectivity mode (with the icon of a diode) and check whether the pins/nodes shall be connected together are connected, and there's no short between other connections and wires.

**Q: My circiut is correctly connected, but the voltage readout is way lower than expected (e.g. below 2V). What's wrong with my setup?**

A: This is a typical phenomenon when you are powering the board with your ESP32/XIAO but not an external power supply. As illustrated in the instruction, you shall power your board with a 5V DC power supply (I bet the usage of the power supply is covered in TECHIN512). The voltage output of Seeeduino XIAO is only 3.3V. If you read the datasheet of LM317 carefully, you shall notice that there's a requirement about the minimum difference between the input and output voltage. Therefore, if the input voltage is not high enough, the output voltage cannot reach your ~2V target, regardless of your resistor selection.

**Q: I've checked my board with the multimetor and the output is correct, but when I'm using the XIAO board to read the analog, I cannot have the correct readings (sometimes reads zero). What should I do?**

A: If you check your XIAO board carefully, you shall notice that there's a tag on one side of it writing "**Seeeduino XIAO ESP32S3**". Therefore, check your PlatformIO setup and see if you've selected the correct board setup. This is **DIFFERENT** from our previous lab's choice which we set up the environment for Atmel platform based XIAO board. Here's the [link of the correct board environment setup](https://docs.platformio.org/en/latest//boards/espressif32/seeed_xiao_esp32s3.html), and you can paste it in your `platformio.ini` file to re-config it.

```
[env:seeed_xiao_esp32s3]
platform = espressif32
board = seeed_xiao_esp32s3
framework = arduino
```

Q: Which pin is pin 1?

A: Usually, you can find it in datasheet. For the TPS793, please follow this figure:
![](image/Lab2_component_selection/TPS793.png)