# PLL on FPGA (via Labview)

This repository contains two implementations of a [phase-locked loop](https://en.wikipedia.org/wiki/Phase-locked_loop) (PLL) on a [FPGA](https://en.wikipedia.org/wiki/Field-programmable_gate_array) (field-programmable gate array). We use the Labview graphical programming environment to generate FPGA binary code and a PC interface.

## Costas loop

The algorithm is a [Costas loop](https://en.wikipedia.org/wiki/Costas_loop), inspired by an [example](https://forums.ni.com/t5/Example-Code/Lock-in-Amplifier-on-LabVIEW-FPGA/ta-p/3500412) by azheng in the Labview Community Examples Forum.

A voltage controlled oscillator (VCO) generates a sine and a cosine. Each is multiplied by the input signal and low pass filtered. The atan2 is calculated to get the phase difference between the VCO and the input signal. A PI controller tries to minimize this phase difference by applying an offset to the VCO.


## Our use of the PLL
We use these PLLs to recover the phase of an analog voltage signal. The signal is a superposition of two or three harmonic oscillations of different frequencies in the 10 kHz to 100 kHz frequency range. 
Three PLLs lock on to the phases of these harmonic signals.
Each PLL updates its phase estimate with each sample of the ADC. We interpolate successive phase values on a faster time scale to reduce jitter in the recovered phase zero crossing at which a TTL trigger is generated.

This is part of the reference generation in our setup for fluorescence-detected [two-dimensional electronic spectroscopy](https://en.wikipedia.org/wiki/Two-dimensional_electronic_spectroscopy) as described in the following publications:

* Sanchayeeta Jana, Simon Durst, Markus Lippitz:
*Fluorescence-Detected Two-Dimensional Electronic Spectroscopy of a Single Molecule*, **Nano Letters**, 24, 12576-12581 (2024), [doi: 10.1038/s41598-023-33654-6](http://dx.doi.org/10.1038/s41598-023-33654-6), [arxiv: 2407.09200](https://arxiv.org/abs/2407.09200)

* Sanchayeeta Jana, Simon Durst, Lucas Ludwig, Markus Lippitz:
*Overcoming experimental obstacles in two-dimensional spectroscopy of a
single molecule*, submitted (2025)

In the second publication, we characterize the operation of the PLL in the NI USB 7856R implementation.


## Implementations

### MyRio

The NI [MyRio](https://en.wikipedia.org/wiki/MyRIO) is based on a Xilinx Z-7010 and contains an FPGA and a real-time PC. Our code implements the PLL at a high level, which is easier to understand, but leaves a lot of optimization to the compiler. The two sub-variants of our implementation allow either 3 channels and a fixed filter or 2 channels and a variable filter. The ADC operates at 500 kHz and 12 bits. The digital filters are those provided by Labview.

More details are [here](MyRio_Implementation.md).

### NI USB-7856

The NI USB-7856R OEM board has a larger FPGA (Kintex-7 160T) and a faster ADC (1 MHz, 16 bit). The PLL algorithm is the same, but is implemented closer to the hardware. We run the core of the PLL in a Single-Cycle Timed Loop (SCTL), which controls the timing more accurately. For this we use the 
[4-wire handshake](https://forums.ni.com/t5/Example-Code/FPGA-Design-Pattern-Four-Wire-Handshake-N-Input-to-M-Output/ta-p/3537293). We have also modified the digital filters provided by Labview to work in an SCTL.

More details are [here](NI_USB_7856R_implementation.md). 

## Labview Version

We are using here the 2018 version of Labview 32bit. You need the Labview FPGA Module and, for MyRio, the Labview MyRio Toolkit. Newer versions should be able to open and compile the files. With newer versions the FPGA Module is also available for 64bit.

## Licence


The code is licensed under the [MIT license](LICENSE). If you use this code for scientific work, please consider citing one of our publications listed above.