# Phase-locked-loop-IC-design


![Wokshop](https://user-images.githubusercontent.com/78468534/127773666-6942ba09-4dd8-488b-af30-fa7b3ed6179b.jpeg)

VSD workshop - Phase Locked Loop(PLL) IC Design!

The following repo is the documentation of learnings and activities done throughout a 2-day workshop on PLL IC Design with SKY130 Technology conducted by VLSI sytem design.


# *Contents*
------------
* [Introduction to PLL](#introduction-to-pll)
  * [Phase Frequency Detector](#phase-frequency-detector)
  * [Charge Pump](#charge-pump)
  * [VCO and Frequency Divider](#vco-and-frequency-divider)
* [Tools Overview and Design Flow](#tools-overview-and-design-flow)
  * [Design Flow](#design-flow)
  * [Skywater PDK](#skywater-pdk)
  * [NGSPICE](#ngspice)
  * [Magic](#magic)
* [Circuit Design and Simulation](#circuit-design-and-simulation)
  * [Design of Components](#design-of-components)
  * [Pre layout Simulations](#pre-layout-simulations)
* [Layout Design](#layout-design)
* [Parasitic Extraction](#parasitic-extraction)
* [Post layout Simulation](#post-layout-simulation)
* [Tapeout](#tapeout)
* [Acknowledgement](#acknowledgement)




---------
## Introduction to PLL
Phase locked loops are important components in any digital circuits. It is responsible for creating a precise clock signal without any noise(frequency or phase).
![PLL-basic-structure](https://user-images.githubusercontent.com/78468534/127774506-b254b925-d629-4f40-8440-e0f332b1e57c.jpeg)

The above diagram shows basic block diagram of the PLL to be implemented.
The functionality of PLL can be decribed as two processes.
* Comparing frequency of reference and ouput(PFD)
* Adjusting frequency to match reference signal(CP and VCO)


#### Phase Frequency Detector
The phase frequency detector(PFD) is responsible for comparing the reference signal given as input and the output signal. Then it should produce output which clearly shows the difference of phase. This phase difference is not just in terms of magnitude but it should also show whether the ouput is leading or lagging behind the reference. The ouput of PFD is in digital form.
#### Charge pump
The CP converts the digital output from PFD to an analog signal. This analog signal is what would control the VCO. The analog ouput from CP is passed through a low pass filter before connecting to the VCO. This low pass filter can help smoothen the signal in addition to stabilizing the feedback loop.
#### VCO and Frequency Divider
Voltage controlled oscillators are the actual parts which produces alternating digital clock signal. The frequency of this clock signal can be controlled by input voltage, hence the name. VCO can be implemented using simple inverters.
A PLL with a frequency divider on its feedback loop is called a clock multiplier PLL. Such a PLL can make clock signals which are multiples of the reference signals.

---------
## Design Flow

Design flow of the PLL IC has the following steps:
1. Specifications of the IC:
2. SPICE level circuit development
3. Pre-layout simulation
4. Layout development
5. Parastic Extraction
6. Post-layout simulation
7. Tapeout


