# External measurement board

As part of the CARROTS project, we develop a MMC prototype using the OwnTech Suite. WP1 develops the hardware prototype. WP2 and WP3 use this prototype to develop and test decentralized control and communication.

To do classic MMC closed-loop control, it is necessary to have external measurement boards to measure the DC voltage $u_{dc}$, DC current $i_{dc}$, phase voltage $v_{p}$, phase current $i_{p}$ and arm currents $i_{u,l}$. Also, the MMC start-up require a resistance + relay system on the DC/AC sides for current limitation. Figure 1 shows the necessary measurements outside of the modules and the position of the correspondent external measurement boards.

![Figure 1: MMC converter circuit with necessary measurement points.](figures/image3.png)

# Board structure

The external measurement board general structure is represented in Figure 2. It is composed of a power and a digital part separated by a galvanic isolation. The power part circuit is shown in Figure 3. It is composed of a power relay, 2 voltage measurements and 1 current measurement. The connectors A-B side is called "upstream" and connectors C-D side is called "downstream". The digital part is composed of a microcontroller (SPIN) that reads the measurements and sends to the controller and other modules via RS485 communication.

![Figure 2: External measurement board structure.](figures/image4.png)

![Figure 3: External measurement board power-side circuit.](figures/image5.png)

# KiCad schematic development

## Power circuit

The power part of the external measurement board is composed of a solid state relay that connects upstream side to downstream side, connectors that connects to the converter circuit and voltage/current sensors.

**Questions left for discussion:**

- **For now I have kept the backplane structure as used for the modules for compatibility, but we also evoked the possibility of using banana connectors.**
- **Added a 1 MΩ resistor R282 between PUPGND and PDOWNGND, so the two grounds are at the same voltage before the relay closes.**

![Figure 4: External measurement board power part.](figures/image6.png)

## Voltage and current sensors

The measurement solution is the same used in the new module board. The voltage raw measurement is acquired using a voltage divisor. The current raw measurement is acquired by an IC current sensor.

Then, both raw measurements are amplified and sent to the digital part of the board using a galvanic isolated amplifier.

Finally, the voltage and current measurements are treated by an analog front-end that is able to change the sensor gain, offset and resolution according to the application.

**Questions left for discussion:**

- **Which value to use in R249 and R254?**

![Figure 5: Voltage divisor for raw voltage acquisition.](figures/image7.png)

![Figure 6: The raw measurement adaption stages for voltage (amplifier and analog front-end).](figures/image8.png)

![Figure 7: The current raw measurement with IC current sensor and amplification.](figures/image9.png)

![Figure 8: The current analog front-end.](figures/image10.png)

![Figure 9: SDA_MCU and SCL_MCU pull-up resistors.](figures/image_pullupR.png)

## SPIN

The SPIN is the microcontroller part of the board. The SPIN from OwnVerter solution is kept but some changes on the pinout were necessary:

- Adapt the pins to the measurements we implemented (Vup, Vdown, IAB)

- Add a command for the relay

![Figure 9: SPIN pinout of external measurement board.](figures/image11.png)

## Communication

The RS485 communication is used to communicate the boards measurement with the controller/other modules of the converter. It can also receive commands to open/close the switch.

CAN and analog communications are also included in the schematic since they are a part of the base OwnTech solution.

The communication RJ45 terminals pins order are modified from latest board versions (TWIST V1.4.3 and OWNVERTER V1.1) to be correspondent to the pins used in the WP4 redesigned modules.

**Questions left for discussion:**

- **A adaptation to communicate with previous board versions is then necessary?**
- **Adaptation to communicate with 3 different buses**

![Figure 10: RJ45 terminals pinout.](figures/image12.png)

![Figure 11: RS485 communication schematic.](figures/image13.png)

![Figure 12: Analog communication schematic.](figures/image14.png)

![Figure 13: CAN communication schematic.](figures/image15.png)

## Auxiliary circuits supply

We adapted the auxiliary circuits supply feed generation of the microinverter project <https://github.com/owntech-foundation/micro-inverter>. The auxiliary circuits are fed by 5 VDGND, 5 VPUPGND and 5VPDOWNGND derived by the 12 VDGND input. The 12 VDGND input must be supplied by an external DC source.

The TPS7420 linear voltage regulator is used on the output of all +5V feeds to filter signal noises and keep the output on the target 5 V voltage.

**Questions left for discussion:**

- **Need to update the notes on consumption of the the supply circuits.**

![Figure 14: Auxiliary supply for PUPGND referenced components.](figures/image16.png)

![Figure 15: Auxiliary supply for PDOWNGND referenced components.](figures/image17.png)

![Figure 16: Auxiliary supply for DGND referenced components.](figures/image18.png)

## Datasheets referencing

There still some elements that have different datasheets reference (in PEBB too) compared to their LCSC number. Also there are some datasheets that are not specified:

**Datasheet referenced does not correspond to the LCSC part:**

**Datasheet not specified:**

- Connectors: U809 (PwrBlade 51722-10602-0020)

Passive elements, mounting holes (H1 to H6), solder jumpers (JP406, JP410, JP417) and the eurocard rails (N101) do not need a datasheet.

## BOM referencing

- No LCSC number: R249 and R254 (5.6 k 0805), and U809 (its value is also ~).

# KiCad Layout

## General structure

The board PCB has a structure divided in two parts, digital (upper) and power (lower), separated by a isolation area of 8 mm as defined by the IEC 60664 standard.

The board dimensions are reduced to 100mmx100mm compared to the usual 100mmx160mm of the OwnVerter/Twist boards.

The board keeps the same railtracks and mount holes for mechanical fixation as the OwnVerter/Twist boards. The 4 lower mount holes positions where adapted to position a safety bow around the board power part, avoiding user direct contact.

In the following sections, certain components positioning details are defined.

![Figure 17: General look on the board layout without tracks.](figures/image19.png)

## Relay

The relay and its activation circuit on digital-side are shown in Figure 18. The relay is placed across the isolation area.

The activation circuit is placed in a certain distance from the relay to allow the placement of the security box.

![Figure 18: Layout of the relay and its activation circuit.](figures/image20.png)

##  Auxiliary supply for PUPGND/PDOWNGND referenced components

The CMT8830U power transformer is used to isolate the 5 VDGND to 5 VPUPGND/5 VPDOWNGND. The CMP6503S isolated power transformer driver is used to drive the transformer. In its datasheet, its recommended positioning with respect to the transformer is as shown in Figure 19.

![Figure 19: CMP6503S datasheet indicated placement.](figures/image21.png)

The TPS7420 linear voltage regulator is used on the output of all +5V feeds to filter signal noises and keep the output on the target 5 V voltage. In its datasheet, its recommended positioning with respect to the input and output capacitors is as shown in Figure 20.

![Figure 20: TPS7420 datasheet indicated placement.](figures/image22.png)

With the guidance of these datasheets indications the placement for the PDOWNGND referenced side was implemented as shown in Figure 21. The driver circuit is distanced from the transformer to allow the placement of the security box.

![Figure 21: Placement of the auxiliary supply referenced to PDOWNGND in the layout.](figures/image23.png)

The placement for the PUPGND part is similar, but the buck circuit was moved to under the switch due to lack of space in the layout power part.

![Figure 22: Placement of the auxiliary supply referenced to PUPGND in the layout.](figures/image24.png)

## Auxiliary supply for DGND referenced components

To pass from 12 VDGND to 5 VDGND voltage, the TPS562202 buck converter is used. The positioning of the commutation cell with respect to the other buck components are done as suggested in the datasheet as shown in Figure 23.

![Figure 23: TPS562202 datasheet indicated placement.](figures/image25.png)

The TPS7420 linear voltage regulator is used on the output of all +5V feeds to filter signal noises and keep the output on the target 5 V voltage. In its datasheet, its recommended positioning with respect to the input and output capacitors is as shown in Figure 20.

![Figure 24: TPS7420 datasheet indicated placement.](figures/image22.png)

The 5VDGND generation layout is shown in Figure 25.

![Figure 25: Auxiliary supply for DGND layout.](figures/image26.png)

## Voltage measurement circuit

The AMC1311x isolated amplifier is used to isolate the raw measurement of the power side from the digital measurement that is used by the MCU. In its datasheet, its recommended positioning with respect to the voltage divisor, input RC filter and decoupling capacitors is shown in Figure 26.

![Figure 26: AMC1311x datasheet indicated placement.](figures/image27.png)

With the guidance of these datasheets indications the placement for both PUPGND/PDOWNGND referenced side was implemented as shown in Figure 27. The resistors where placed to avoid letting different potentials close to each other.

![Figure 27: Placement of the voltage measurement circuit referenced to PUPGND in the layout.](figures/image28.png)

The analog-front end gets this digital output from the amplifier and adapts it according to user set gain, offset and resolution. The placement of the analog front-end for the 2 voltage measurements and current measurement was kept near the top part of the board as in the module boards developed by WP4, shown in Figure 28.

![Figure 28: Placement of the analog front-end for voltage and current measurements in the layout.](figures/image29.png)

## Current measurement circuit

The ACS724 isolated current sensor is used to acquire the raw current measurement passing from A to B connectors of the board. There is no recommended layout position in its datasheet, so the components where placed as close as possible to the pins. To have a smaller current path, the current sensor was placed the closest as possible to the relay I+ pin and VDOWN connector.

Similarly to the voltage measurement, the AMC1311x isolated amplifier is used to isolate the raw measurement of the power side from the digital measurement that is used by the MCU. In its datasheet, its recommended positioning with respect to the voltage divisor, input RC filter and decoupling capacitors is shown in Figure 26.

With the guidance of these datasheets indications, the placement of the current measurement circuit was implemented as shown in Figure 29.

![Figure 29: Placement of the current measurement circuit referenced to PDOWNGND in the layout.](figures/image30.png)

# Wiring development

## +12VDGND, +5VDGND, +5VPUPGND and +5VPDOWNGND feeding

The components feeding in the digital part was concentrated in the In2 layer. The widths of each one were estimated using the KiCad width calculator considering $\Delta T = 10 °C$ and H = 35 µm.

### +12VDGND width

To the left side, the +12VDGND input supplies the MOSFET that drives the relay and the realy. The relay consumption of the 12 V input is of 67 mA according to the microinverter project notes. Given this, we estimated:

| Current (A) | Conductor length (mm) | Calculated width (mm) |
|:-------------------------------:|:---------------------:|:---------------------:|
|0.067|72|0.0188|

To give a safety marge for the relay consumption and to use TWIST/SPIN usual values for feeding traces width, we used 0.25 mm width for the conductor feeding the relay + relay MOSFET.

To the right side, the +12VDGND input supplies all the other digital side components, since it feeds the +5VDGND and +5VDCLEARGND and the power side comonents, since the +5VDGND feeds the +5VPUPGND and +5VPDOWNGND. We make a list of its main components consumption in current:

| Component | Current (mA) |
|:----------|:-----------:|
| **+5VPUPGND loads** (fed through U802 + TR801 + U804) | |
| U205 AMC1311 isolated amplifier high-side supply (IDD1) | 9.7 |
| R38 transformer pre-load (5 V / 4.7 kΩ) | 1.1 |
| *Subtotal +5VPUPGND* | *10.8* |
| **+5VPDOWNGND loads** (fed through U803 + TR802 + U805) | |
| U207 AMC1311 isolated amplifier high-side supply (IDD1) | 9.7 |
| U211 AMC1311 isolated amplifier high-side supply (IDD1) | 9.7 |
| U801 ACS724 current sensor (ICC) | 15 |
| R58 transformer pre-load (5 V / 4.7 kΩ) | 1.1 |
| R280 + R281 divider on ACS724 output | 0.2 |
| *Subtotal +5VPDOWNGND* | *35.7* |
| **+5VDCLEANGND loads** (fed through U806) | |
| U205 AMC1311 isolated amplifier low-side supply (IDD2) | 8.1 |
| U207 AMC1311 isolated amplifier low-side supply (IDD2) | 8.1 |
| U211 AMC1311 isolated amplifier low-side supply (IDD2) | 8.1 |
| AFE801, AFE802, AFE807 analog front-ends (estimate, all three) | 100 |
| *Subtotal +5VDCLEANGND* | *124.3* |
| **+5VDGND loads** (fed by U14 buck) | |
| U401 SPIN (estimate) | 100 |
| U402 CA-IF1051S CAN transceiver | 80 |
| U405 CA-IF4850HS RS485 transceiver (driving 54 Ω) | 70 |
| U406 MCP6487 dual op-amp (2 × 0.72 mA) | 1.4 |
| R431 + R432 I2C pull-ups (both lines low) | 2.1 |
| U802 CMP6503S input (+5VPUPGND subtotal, η ≈ 52 %) | 22 |
| U803 CMP6503S input (+5VPDOWNGND subtotal, η ≈ 77 %) | 49.1 |
| U806 TPS7A2050 input (= +5VDCLEANGND subtotal) | 124.3 |
| ***Total +5VDGND*** | ***448.9*** |


The CMP6503S input currents are calculated as $I_{in} = I_{out} \cdot V_{out} / (\eta \cdot V_{in})$, with $V_{out} \approx 5.3$ V after the rectifier and $\eta$ read from the CMP6503S datasheet curves (Fig. 8.7 and 8.11, measured with the CMT6870N transformer). The linear voltage regulators TPS7A2050PDBVR input current is equal to their output current. The TPS562202 input current is calculated as $I_{12V} = I_{5V} \cdot 5.07 / (\eta \cdot 12)$, with $\eta \approx 93 \%$ and $I_{5V} = 448.9$ mA from the TPS562202 datasheet (Fig. 7-14, $V_{in}$ = 12 V, $V_{out}$ = 5 V).

This gives us a total current of 0.204 A for the right side branch.

We also could do a estimation of possible current consumption considering 3 W, which gives 3/12 = 0.25 A, which is very close to the value from the datasheets.

The current of 0.25 A is taken into account since it is the biggest. With this current, we estimated the width:

| Current (A) | Conductor length (mm) | Calculated width (mm) |
|:-------------------------------:|:---------------------:|:---------------------:|
|0.25|23|0.11|

To give a safety marge and to use TWIST/SPIN usual values for feeding traces width, we used 0.5 mm width for the conductor feeding the TPS562202 component.

### +5VDGND width

Since the +5VDCLEARGND consumption is directly connected to +5VDGND with a copper zone, we consider only 324.6 mA consumption to define the wires width.

| Current (A) | Conductor length (mm) | Calculated width (mm) |
|:-------------------------------:|:---------------------:|:---------------------:|
|0.324|19|0.16|

To give a safety marge and to use TWIST/SPIN usual values for feeding traces width, we used 0.5 mm width for the conductor feeding the branches RS485 transceiver + SPIN/CAN + Analog + CMP6503S + transformer D-PUP D-PDOWN.

### +5VPUPGND and +5VPDOWNGND width

As the division of load between the two +5VDGND branches are quite equilibrated, from this point ownwards, we consider 0.5 mm or 0.25 mm width are sufficient.

### +5VDCLEARGND width

The +5VDCLEARGND consumption is 124.3 mA, so we calculate the width.

| Current (A) | Conductor length (mm) | Calculated width (mm) |
|:-------------------------------:|:---------------------:|:---------------------:|
|0.125|137|0.0444|

To give a safety marge and to use TWIST/SPIN usual values for feeding traces width, we used 0.5 mm width for the conductor feeding of the +5VDCLEARGND branches.

The resulting feeding wired for the digital and power side components is given on Figures 30 and 31. The feeding on the digital side is on In2 layer and the feeding on the power side is on In1 layer.

![Figure 30: Digital feed wiring.](figures/image_wiring_Dfeed.png)

![Figure 31: Power feed wiring.](figures/image_wiring_Pfeed.png)

## Power circuit

The power circuit composed by relay + current and voltage measurements (see Figure 4) needs to have its wiring compatible to the current passing by each branch.

### VDOWN to current sensor

Using a copper plane of 17 mm width (Y axis) and 10 mm length (X axis), we do a path between VDOWN terminal and the current sensor as in Figure 32.

![Figure 32: Power path between VDOWN and I sensor.](figures/image_wiring_VDOWNIsensor.png)

### Current sensor to relay

Using a copper plane of 17 mm width (Y axis) and 22 mm length (X axis), we do a path between the current sensor and the relay as in Figure 33.

![Figure 33: Power path between I sensor and relay.](figures/image_wiring_IsensorRelay.png)

### Relay to VUP terminal

Using a copper plane of 17 mm width (Y axis) and 45 mm length (X axis), we do a path between the relay and the VUP terminal as in Figure 33.

![Figure 34: Power path between relay and VUP terminal.](figures/image_wiring_RelayVUP.png)

### Voltage measurement voltage divider

The path to the voltage measurement voltage divider consumes some current from the main current path. Considering worst can scenario where VUP or VDOWN voltage peak is 800 V, the current demand is related to the voltage divider total impedance (see Figure 5).

Using that $R = 5 \cdot 0.33 = 1.65 M \Omega$, the current demand would be $I = \frac{800}{1.65\cdot 10^6}=0.485$ mA

Using the calculator again with this current and the lengths between the relay and the voltage dividers. Remark, we consider that the inner copper layer width is 17.5 µm.

| Current (mA) | Conductor length (mm) | Calculated width (um) |
|:-------------------------------:|:---------------------:|:---------------------:|
|0.485|72|41.9|
|0.485|32|41.9|

We use 0.5 mm width for safety margin. The resulting wiring for the power circuit is given on Figure 35.

![Figure 35: Power circuit wiring.](figures/image_wiring_Pcircuit.png)

## Signals

All other signals are wired in the front and bottom layers. The resulting wiring is shown in Figure 36.

![Figure 35: Signals wiring.](figures/image_wiring_signals.png)

## Used vias

According to JLCPCB, the minimum hole diameter of vias for the default configuration is 0.3 mm. The via diameter should be +0.15 mm than the hole size, so 0.45 mm.

We used the via size of 0.3 mm/0.45 mm for thermal dissipation of TPS562202. Other vias are of standard 0.4 mm/0.8 mm size proposed in KiCad.

## Layers copper thickness

According to JLCPCB, the default copper thickness for outer layers is 1 oz (= 35 µm) and for inner layers is 0.5 oz (=17.5 µm).