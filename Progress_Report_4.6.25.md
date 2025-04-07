Strain Gauge Amplifier Project with 4-20mA Output: Progress Report
Project Overview
We've designed a complete signal conditioning system that converts a strain gauge's resistance changes into a standard 4-20mA current loop output suitable for industrial PLC systems. The design accounts for your 350Ω strain gauge with ±10% resistance variation and provides appropriate amplification and signal conditioning.
System Components

Wheatstone Bridge Stage

Configuration: Four-resistor bridge with one active strain gauge (350Ω nominal)
Supply: 5V (derived from 48V system supply)
Output: Small differential voltage proportional to strain gauge resistance change


Instrumentation Amplifier Stage (INA333)

Initial amplification of bridge differential voltage
REF pin connected to 2.5V for mid-supply referencing
Output range: Approximately 1.75V to 2.5V for the ±10% resistance change


Signal Conditioning Stage (OPA336)

Non-inverting amplifier configuration with gain ≈ 1.5
R1 = 10kΩ (to ground), R2 = 5kΩ (feedback)
Output range: Approximately 2.6V to 3.8V


Voltage-to-Current Converter Stage

OPA336 driving a 2N2222 transistor
50Ω precision sense resistor
24V loop supply for industrial compatibility
Output: 4-20mA current proportional to strain



Key Learnings and Observations

PSpice Simulation Considerations

Convergence issues were addressed by adjusting simulation parameters
Care needed with op-amp rail-to-rail limitations when designing output stages


Op-Amp Selection Insights

The OPA336 has output limitations (can't swing fully to rails)
Low power consumption makes it suitable for battery or energy-efficient applications
Limited bandwidth (100kHz) is still adequate for strain gauge measurements


Signal Chain Optimization

Proper reference voltage selection is critical for maximizing dynamic range
Multiple amplification stages help achieve desired signal conditioning flexibility



Next Steps and Recommendations

1. Calibration Procedure Development

Develop a calibration procedure using known strain values
Implement trim potentiometers for fine-tuning the output range


2. Temperature Compensation

Consider adding temperature compensation if the system will operate in varying environments
Could be implemented with a temperature sensor and additional circuitry


3. Protection Features

Add reverse polarity and overvoltage protection for field deployment
Consider optical isolation between sensing and output stages


4. PCB Layout Considerations

Keep analog signal paths short and separated from digital/power circuits
Use a ground plane for stable reference
Consider Kelvin connections for bridge resistors


5. Documentation

Create a detailed calibration procedure document
Document power supply requirements and signal ranges



This completes the basic design process for your strain gauge amplifier with 4-20mA output. The system should be ready for initial testing and calibration once implemented in PSpice and eventually on a physical circuit.


**** PSpice Schematic for Voltage-to-Current (4-20mA) Converter ****

** Component List **
* U3 - OPA336 Op-Amp (V-to-I converter)
* Q1 - 2N2222 NPN Transistor (or equivalent)
* R_SENSE - 50Ω, 1% precision resistor
* R_LOAD - 250Ω (simulates PLC input impedance)
* R5 - 10kΩ (input resistor)
* R6 - 2.2kΩ (scaling resistor)
* R7 - 470Ω (base resistor)
* VDC_LOOP - 24V DC supply for current loop
* C1 - 0.1μF (optional, for stability)

** Connection Instructions for PSpice **

1. POWER SUPPLY:
   * Place a 24V DC source (VDC_LOOP)
   * Connect the positive terminal to a node labeled "LOOP_POS"
   * Connect the negative terminal to ground (GND)

2. OP-AMP SETUP:
   * Place OPA336 (U3)
   * Connect pin 7 (V+) to "LOOP_POS" (24V)
   * Connect pin 4 (V-) to ground
   * Connect OPA336 output (pin 6) to R7

3. INPUT CONNECTION:
   * Connect the output from your existing circuit (OPA336 output) to R5
   * Connect the other end of R5 to the non-inverting input (pin 3) of U3
   * Place an offset voltage divider (if needed) using two resistors between LOOP_POS and GND, with the midpoint connected to pin 3 through another resistor

4. CURRENT OUTPUT STAGE:
   * Connect R7 to the base of Q1 (2N2222)
   * Connect the collector of Q1 to LOOP_POS
   * Connect the emitter of Q1 to R_SENSE
   * Connect the other end of R_SENSE to ground
   * Connect the junction of Q1's emitter and R_SENSE to the inverting input (pin 2) of U3
   * Connect R6 from the inverting input (pin 2) to the non-inverting input (pin 3)

5. LOAD CONNECTION:
   * Connect R_LOAD between LOOP_POS and the collector of Q1
   * This simulates the PLC input load

6. SIMULATION SETUP:
   * Set up a DC sweep analysis
   * Sweep the strain gauge resistance from 315Ω to 385Ω
   * Monitor the voltage across R_LOAD and calculate the current (I = V/R)

** Calibration Notes **
* The current through R_SENSE can be calculated as: I = V_sense / R_SENSE
* To adjust the 4mA offset: Modify the voltage divider feeding into pin 3
* To adjust the span (16mA range): Change the value of R6 
* With the values provided, the circuit should produce approximately:
  - 4mA when input is at minimum (2.6V)
  - 20mA when input is at maximum (3.8V)

** Simulation Tips **
* If convergence issues occur, try:
  - Adding a small capacitor (C1) between pin 2 and ground
  - Using .OPTIONS RELTOL=0.01 and ABSTOL=1µA
  - Setting MODEL parameters for the transistor appropriate for your simulation