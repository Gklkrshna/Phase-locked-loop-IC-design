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
  * [Tool Setup](#tool-setup)
   * [SKY130 PDK](#sky130-pdk)
   * [NGSPICE](#ngspice)
   * [Magic](#magic)
* [Circuit Design and Simulation](#circuit-design-and-simulation)
  * [Specifications](#specification)
  * [Frequency Divider Circuit](#frequency-divider-circuit)
  * [PFD Circuit](#pfd-circuit)
  * [Charge Pump Circuit](#charge-pump-circuit)
  * [VCO Circuit](#vco-circuit)
  * [PLL Circuit](#pll-circuit)
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
## Tools Overview and Design Flow
#### Design Flow

Design flow of the PLL IC has the following steps:
1. Specifications of the IC
2. SPICE level circuit development
3. Pre-layout simulation
4. Layout development
5. Parastic Extraction
6. Post-layout simulation
7. Tapeout

#### Tool Setup

##### SKY130 PDK
The SKY130 are set of 180nm-130nm technology based process nodes and PDKs(Process Design Kits), provided for free. The content provided in this pdk include:
* io-input-output
* pr-primitives
* sc-standard cell
* hd-high density
* hs-high speed
* lp-low power
* hdll-high density low leakage

##### NGSPICE
The ngspice is an open-source electronic circuit simulator widely used in ciruit design and analog VLSI domain. Ngspice is very simple to get in linux systems. The following terminal command can be used to install it in a linux system.

            sudo apt-get install ngspice

##### Magic
Magic is an open-source VLSI layout tool. It is mainly used for making the layout and the parasitic extraction which follows. For SKY130, the latest version of magic needs to be installed and this can be done by directly compiling and "making" the source code. Terminal commands for the same are given below.

           sudo apt-get update && sudo apt-get upgrade      //update OS
           git clone git://opencircuitdesign.com/magic      //clone magic repository
           sudo apt-get install csh                         //install csh shell if it is absent
           cd magic
           ./configure                                      //run configure script
           make                                             //run make command to compile
           sudo make install                                //install magic


Additionally, many of the files used in this workshop where provided through a github repository.  
![git_lakshmi](https://user-images.githubusercontent.com/78468534/127781783-f22154e9-a5ff-4eae-8128-38edf4433ac5.jpeg)


---------
## Circuit Design and Simulation

#### Specifications
As mentioned in the design flow, every design starts with determining the specifications. The specifications for PLL to be designed are:

* Corner - 'TT' (_Typical-Typical_)
* Supply Voltage - 1.8V
* Room Temperature
* VCO mode and PLL mode
* Input F<sub>min</sub>=5Mhz; F<sub>max</sub>=12.5Mhz
* Multiplier - 8x
* Jitter(RMS) < ~20ns
* Duty Cycle - 50%

#### Frequency Divider Circuit
A simple 2x frequency divider circuit can be obtained by using a single D-flipflop whose output is fed back to its input after passing through an inverter. Cascading 3 such flops can give you 8x divider.
This simple circuit can be drawn as:  
![FD_circuit](https://user-images.githubusercontent.com/78468534/127781480-b09756aa-a4ce-48e4-8164-4fad67cf1f7d.jpeg)

Writing this circuit as a spice file:  
![FD_spice](https://user-images.githubusercontent.com/78468534/127781792-3465c9c8-3245-4b77-ac2f-e5bde8e3e9cc.jpeg)

*Pre-layout Simulation*  
The pre-layout simulation of spice model can be done using the command:

                              ngspice <spice_file_name>


![FD_prelay_sim](https://user-images.githubusercontent.com/78468534/127782690-bc1b6585-a9a2-4702-975f-15f574a865cc.jpeg)
_FD Prelayout Simulation Result_

#### PFD Circuit
The PFD circuit is designed such that, square(digital) signals with pulse width proportional to phase difference are produced at output. Also two different outputs are used to distinguish between cases when output is leading reference signal and lagging behind reference signal.

Given below are the PFD circuit..  
![PFD_circuit](https://user-images.githubusercontent.com/78468534/127782010-b21f76ed-6bed-4406-bfd0-2c5fca9838ac.jpeg)

and the spice file.  
![PFD_spice](https://user-images.githubusercontent.com/78468534/127782024-c29ba837-832e-431c-b468-786f3979b9eb.jpeg)

*Pre-layout Simulation*  

![PD_prelay_sim](https://user-images.githubusercontent.com/78468534/127782705-2d819680-ab57-4769-8084-2271b544e5b7.jpeg) ![PFD_prelay_graph](https://user-images.githubusercontent.com/78468534/127783258-83a021c6-6241-43df-9a3f-a9cfcb31500e.jpeg)


_PFD Prelayout Simulation Results_

#### Charge Pump Circuit
The charge pump circuit with modification considering the leakage current is  
![CP_circuit](https://user-images.githubusercontent.com/78468534/127782045-6a5b2df2-13fd-4456-9337-6b2af6604d05.jpeg)  
and the spice file is  
![CP_spice](https://user-images.githubusercontent.com/78468534/127782052-9781fe6c-94e5-4cf3-8157-677d27ed436d.jpeg)

*Pre-layout Simulation*  


![CP_prelay_sim](https://user-images.githubusercontent.com/78468534/127782713-b5eaa388-ccfe-4447-8053-d392bf38309b.jpeg) 
_CP Prelayout Simulation Result_

#### VCO Circuit
The VCO circuit is realised with a current starved 3 inverter circuit. Using this method, the delay of inverter can be controlled and thereby the frequency of output clock.
                          frequency of clock = 1/(2 * delay of inverter * no: of inverters)
                          
The circuit is:  
![VCO_circuit](https://user-images.githubusercontent.com/78468534/127782614-ed6b8289-cf29-4cd9-bfe7-078176fe6c26.jpeg)

and the spice model is  
![VCO_spice](https://user-images.githubusercontent.com/78468534/127782626-a74a43c2-fc10-4d88-b3e6-d36360bbb180.jpeg)

*Pre-layout Simulation*  

![VCO_prelay_sim](https://user-images.githubusercontent.com/78468534/127782718-f6cbddc3-54aa-4047-8ff8-c0fef91b9921.jpeg)  
_VCO Prelayout Simulation Result_

#### PLL Circuit
The final PLL circuit is the joining of all the other blocks. However in addition to individual simulations, the PLL as a whole also need to be simulated. For this we create the spice file by calling the individual circuits as subcircuits.  
![PLL_spice](https://user-images.githubusercontent.com/78468534/127783331-469f1c70-0ca4-421c-a20b-e7afc3ff4296.jpeg)

*Pre-layout Simulation*  
![PLL_prelay_sim](https://user-images.githubusercontent.com/78468534/127783351-b3ff364a-3691-485e-b655-543d0290a1a2.jpeg)
![PLL_prelay_graph](https://user-images.githubusercontent.com/78468534/127783355-32111d1c-00c2-4697-a09f-596713cf9852.jpeg)  
_PLL simulation results_

---------
## Layout Design
Layout Design is the part in which the circuit is converted to polygons which can be made into GDSII format. Here layout design is done using the Magic tool. The layouts are saved with ".mag" extensions. These files can be opened with command:

                                          magic -T <technology_file_name> <layout_file_name>

Shown below are examples of terminal commands:  
![Magic_commands](https://user-images.githubusercontent.com/78468534/127783264-96276c70-a604-4f9d-a261-3f6b7b3dc750.jpeg)


The layouts for each circuits are given below:

![FD_layout](https://user-images.githubusercontent.com/78468534/127783517-afba527a-d7b5-42e4-8bd7-bbd58d6ca425.jpeg)  
_FD Layout_

![PFD_layout](https://user-images.githubusercontent.com/78468534/127783518-8e061e34-1910-452a-bd0d-c9ccaf20e062.jpeg)  
_PFD Layout_

![CP_layout](https://user-images.githubusercontent.com/78468534/127783521-f3857e3b-5bdc-4708-b0c7-dc6f8e644cb6.jpeg)  
_CP Layout_

![VCO_layout](https://user-images.githubusercontent.com/78468534/127783524-5d189d3e-773a-497f-a63e-3962dd442a0b.jpeg)  
_VCO Layout_

After making each layout, these layouts can be instantiated to make the final PLL layout. This is done using _File > Place Instance_ option.  
![PLL_layout](https://user-images.githubusercontent.com/78468534/127783571-8c9939d1-c883-4e21-a04e-97d139257e1e.jpeg)  
_PLL Layout_










