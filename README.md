# Keyboard PCB Guide <!-- omit in toc -->

## Introduction <!-- omit in toc -->

Frustrated from the lack of documentation available on the internet, I decided to make my own keyboard PCB making guide with updated resources. Aside from the main guide, there will also be guides on other components such as USB-C and TRRS conectors.

## Table of Contents <!-- omit in toc -->

## PCB Guide

### Step 0: Setup

Here is a list of things that you should download and install (if applicable):

- [KiCAD](http://kicad-pcb.org/)
- Component and Footprint Libraries
    - to make things streamlined, I've decided to compile all libraries I used [here]

### Step 1: Schematics

Run KiCad and press `Ctrl + N` to create a new project. You can enter any name for the project but for this guide, I will be using `stm4x4`.

A new window will pop up, listing 2 files. Double click the `.sch` file and a new window with an engineering sheet will pop up that will look like this:

*[image]

From here on, we will be referring to this window as `Eeschema`.

#### Step 1.0: Adding Libraries

Before we start doing anything, we have to link our libraries to the project. To do this, you click `Preferences > Manage Symbol Libraries` to open up the `Symbol Libraries` window.

Click on `Browse Libraries...` and then navigate to the directory where you stored the component libraries. Select all the libraries and click `Open`.

Once all libraries are added, click on `OK` to go back to `Eeschema`.

#### Step 1.1: Placing Components

Now that our libraries are linked, we can start placing the components on `Eeschema`. Our components are represented as "symbols" in `Eeschema`.

Press `A` to open up the window for choosing symbols, find and select our microcontroller (`MCU_ST_STM32F0 > STM32F042K6Tx`), and place it anywhere.

##### Microcontroller

This is, in general, is a symbol. The `U?` is the reference field for the symbol and `STM32F042K6Tx` is the value field which describes the component.

For now, let's change the reference field to something unique. Hover over `U?`, press `E`, and then change `U?` to `U1` and click `OK`.

`NOTE`: Since this guide uses an STM32 chip, specifically an STM32 chip with crystal-less USB, there are components that are not required such as terminating resistors or an external crystal. Because of that, you may want to check out the [catalog] and check out the other guides there.

##### Eeschema Basics

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

##### Decoupling Capacitors

Next, we add the decoupling capacitors (`keyboard_parts > C`). You have to consult the microcontroller's datasheet to know how to wire it up. Luckily, I have played around with this chip and found that (2) 0.1uF and (1) 4.7uF caps are enough for a simple board like this.

Don't forget to change the value fields! For the reference fields, we can leave it as is for now.

##### Regulator

Since our microcontroller runs on 3.3V and USB only provides 5v, we have to use a regulator to transform the voltage.

Add a linear dropout regulator (`Regulator_Linear > LD1117S33TR_SOT223`) as well as 2 capacitors. Just like the microcontroller, you have to consult the regulator's datasheet to know how to wire it up.

##### DFU Circuit

The DFU circuit consists of a button for reseting the chip and a button for entering DFU (for STM32 only).

Add two tact switches (`keyboard_parts > SW_PUSH`) and a resistor (`keyboard_parts > R`) and wire it up like this.

The BOOT0 button is required for you to be able to enter DFU mode without using software. During startup or power reset, the STM32 chip checks if BOOT0 is high/connected to VCC. If it is, then STM32 enters DFU mode instead of going to user code.

##### USB

For this guide, I will be using a USB mini B connector. For USB C, refer to [this] guide.

Add a USB symbol (`keyboards_parts > USB_mini_micro_B`) and press `Q` to add a no connection flag to ID.

Because we use an STM32F0x2 chip, we do not need any terminating resistor



### Resources

#### Component Libraries
  * [Keyboard Parts Symbols](https://github.com/tmk/kicad_lib_tmk/tree/master)
  * [MX/Alps Switch Symbols](https://github.com/ai03-2725/MX_Alps_Hybrid.pretty/tree/master/Schematic%20Library)
  * [WS2812B LED Symbols](https://github.com/madworm/WS2812B.pretty/tree/falkartis-symbol/Schematic-Symbol)
  * [Official KiCad Symbols](https://kicad.github.io/symbols/) - resistors, diodes, etc.
  * [Pro Micro Symbol](https://github.com/keebio/keebio-components/tree/master)

#### Footprint Libraries
  * [Keyboard Parts Footprints](https://github.com/tmk/keyboard_parts.pretty/tree/master)
  * [MX/Alps Switch Footprints](https://github.com/ai03-2725/MX_Alps_Hybrid.pretty/tree/master)
  * [Keebio's Parts](https://github.com/keebio/Keebio-Parts.pretty/tree/master) - great for split boards and other stuff
  * [ai03's Parts](https://github.com/ai03-2725/Voyager65/tree/master/locallib.pretty) - awesome mounting hole footprint
  * [Nexperia](https://github.com/coarse/Nexperia/tree/master/nexperia.pretty) - SOT143B footprint for PRTR5V0U2X,215
  * [USB Type C](https://github.com/ai03-2725/Type-C.pretty/tree/master)

#### Other Guides
  * [Ruiqimao's Keyboard PCB Guide](https://github.com/ruiqimao/keyboard-pcb-guide#schematics)
  * [ai03's Wiki](https://kbwiki.ai03.me/)