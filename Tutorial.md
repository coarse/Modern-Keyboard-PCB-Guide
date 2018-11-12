# Keyboard Firmwares    

# Table of Contents <!-- omit in toc -->

---

## Preface

This tutorial aims to equip you, the reader, with knowledge on making a minimal keyboard PCB with onboard components. It covers making the schematics, the pcb design, exporting gerbers, and also making a bill of materials (BOM) for the components.

## Chapter 0: Setup

Here is a list of things that you should download and install (if applicable):

- [KiCAD](http://kicad-pcb.org/)
- [Component and Footprint Libraries](https://github.com/coarse/Modern-Keyboard-PCB-Guide#resources)
    - to make things streamlined, I've decided to compile all libraries I used for this guide [here]()
- Microsoft Excel, OpenOffice Calc, or Google Spreadsheet

## Chapter 1: Schematics

Run KiCad and press `Ctrl + N` to create a new project. You can enter any name for the project but for this guide, I will be using `stm4x4`.

A new window will pop up, listing 2 files. Double click the `.sch` file and a new window titled `Eeschema` will pop up. This is where we will be doing all out schematic work.

[pic]

### Chapter 1.0: Adding Libraries

Before we start with anything, we have to link our libraries to the project. To do this, you click `Preferences > Manage Symbol Libraries` to open up the `Symbol Libraries` window.

[pic]

Click on `Project Specific Libraries` and then on `Browse Libraries...`. Navigate to the directory where you stored the component libraries, select all of them, and then click `Open`.

Once all libraries are added, click on `OK` to go back to `Eeschema`.

### Chapter 1.1: Placing Components

Now that our libraries are linked, we can start placing the components on `Eeschema`. Our components are represented as "symbols" in `Eeschema`.

[pic]

Press `A` to open up the window for choosing symbols, find and select our microcontroller (`MCU_ST_STM32F0 > STM32F042K6Tx`), and place it anywhere.

#### Microcontroller

This is, in general, is a symbol. The `U?` is the reference field for the symbol and `STM32F042K6Tx` is the value field which describes the component.

[pic]

For now, let's change the reference field to something unique. Hover over `U?`, press `E`, and then change `U?` to `U1` and click `OK`.

`NOTE`: Since this guide uses an STM32 chip, specifically an STM32 chip with crystal-less USB, there are components that are not required such as terminating resistors or an external crystal. Because of that, you may want to check out the [catalog] and check out the other guides there.

#### Eeschema Basics

Before you move on with the other components, it is important to at least know how to make your way around `Eeschema`. Here is a cheat sheet you can use:

```
A: Add symbol
P: Add power flag
Q: Add no connect flag
H: Add label
Ctrl+H: Add global label
W: Begin wire

Del: Delete highlighted item/symbol
Esc: Cancel action

M: Move highlighted item
G: Move symbol while keeping wires attached
E: Edit highlighted item

R: Rotate highlighted item
X: Flip symbol along the X-axis
Y: Flip symbol along the Y-axis
```

#### Global Labels and Power Flags

To make our life easier, we will make use of global labels and power flags. For now, just put the proper power flags and global labels for the pins that have fixed functions such as `BOOT0`, `RESET`, etc.

[pic]

The power flags will be used in almost all the circuits. `RESET` and `BOOT0` will be use in the [DFU Circuit]. `USB_DP` and `USB_DM` will be used for the USB datalines.

#### Decoupling Capacitors

Next, we add the decoupling capacitors (`keyboard_parts > C`) for our microcontroller.

[pic]

Normally you would want to check your microcontroller's datasheet to find the right setup. However, I was told that (2) 0.1uF and (1) 4.7uF capacitors are enough for a simple keyboard using this chip and indeed it does work.

Don't forget to change the value fields to match the capacitance! For the reference fields, we can leave it as is for now.

#### DFU Circuit

The DFU circuit consists of a button for reseting the chip and a button for entering DFU. The reset button is pretty much universal however the BOOT0 part of the circuit is specific to the STM32 line of chips.

[pic]

Add two tact switches (`keyboard_parts > SW_PUSH`) and a resistor (`keyboard_parts > R`), change its value field to `10k` and wire it up. No need to change the reference fields.

#### USB

For this guide, I will be using a USB mini B connector because of its simple implementation. For USB C, you may refer to the [this guide].

[pic]

Add a USB symbol (`keyboards_parts > USB_mini_micro_B`) and a no connection flag to `ID` by pressing `Q` and place the x mark on `ID`.

[pic]

Wiring this up is pretty much straightforward. Connect `VUSB` to its proper power flag. For the data lines `D-` and `D+`, we connect them to `USB_DM` and `USB_DP`, which we connected to `PA11` and `PA12` earlier. `GND` and `SHIELD` connect to the ground power flag.

#### Power Circuit

Since our microcontroller runs on 3.3V and USB provides 5V, we have to use a regulator to transform the voltage. We will be using an LD1117 with a fixed output of 3.3V.

[pic]

Add the regulator (`Regulator_Linear > LD1117S33TR_SOT223`) as well as 2 capacitors (`keyboard_parts > C`) for the regulator. Don't forget the value appropriate value field for the capacitors.

Just like the microcontroller, you have to consult the regulator's datasheet to know how to wire it up.

#### Switch Matrix

Finally, we can go and make the actual keyboard part of this PCB. In case you didn't notice from the file name, we will be making a 4x4 macropad to really maximize the switch matrix.

If you don't know how a switch matrix works, you can read a really in-depth post about it [here](http://blog.komar.be/how-to-make-a-keyboard-the-matrix/).

[pic]

Add the switch (`MX_Alps_Hybrid > MX-1U-NOLED`) to the schematic a diode (`keyboard_parts > D`) right below the switch and wire it up.

[pic]

Select all the symbols (drag while clicking to make a selection box) and press `Ctrl+C` to copy the symbols. Paste 15 more copies and arrange them in a 4x4 grid fashion and wire them up and add global labels for the columns and rows. And we're done!

[pic]

Now, you might be thinking, how is it "done" if the matrix isn't connected to the microcontroller yet? Well, we're gonna do that part after laying out the footprints. That way, we know which pins to use to be able to have clean.

### Chapter 1.2: Annotating the Schematic

At this point, we've got all of these components with a bunch of "?" in their reference field. What we're gonna have to do next is to change them so that each reference field is unique. This process is called annotating.

Annotating the schematic can be done manually, like what we did to `U1`, or automatically. For the rest of the components, we will be doing it automatically.

[pic]

Click the icon with the paper and pencil, then click `Annotate` on the window that pops up. If done right, our schematic should now be properly annotated. However, your schematic may have different annotations especially if your symbols are arranged differently.

[pic]

If you want to redo all the annotations, click the icon again and click `Reset existing annotations` before clicking on `Annotate`. This will clear all annotations and then annotate them again.

### Chapter 1.3: Assign Footprints

Once we have our schematic down, we have to assign footprints to all the symbols. To do this, click on the icon right beside the ladybug and the `Assign Footprints` window will pop up.

[pic]

Chances are the 1st pane in the window will be empty. This is because we have not yet linked any footprint library to our project. To do so, click on `Preferences > Manage Footprint Libraries...` to open up the `Footprint Libraries` window.

[pic]

Click on `Project Specific Libraries` and then on `Browse Libraries...`. Navigate to the directory where you stored the footprint libraries and add them one by one.

[pic]

Once you have all the libraries added, click `OK` on the `Footprint Libraries` window and give yourself a pat on the back. KiCAD's UI is horrible but you managed to survive.

[pic]

Finally, we can assign footprints. Here is a table of what footprint you should assign to each kind of symbol.

[table]

### Chapter 1.4: Generating Netlist

The next step is to export this configuration into a netlist.

Click on the icon with "NET" on it and then click `Generate Netlist`. A window should pop up and just click `Save` without changing anything.

## Chapter 2: PCB Layout