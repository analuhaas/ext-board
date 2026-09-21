#  Goal of this document

As part of the CARROTS project, we develop a MMC prototype using the OwnTech Suite. WP1 develops the hardware prototype. WP2 and WP3 use this prototype to develop and test decentralized control and communication.

To do classic MMC closed-loop control, it is necessary to have external measurement boards to measure the DC voltage $u_{dc}$, DC current $i_{dc}$, phase voltage $v_{p}$, phase current $i_{p}$ and arm currents $i_{u,l}$. Also, the MMC start-up require a resistance + relay system on the DC/AC sides for current limitation. Figure 1 shows the necessary measurements outside of the modules and the position of the correspondent external measurement boards.

![Figure 1: MMC converter circuit with necessary measurement points.](figures/image3.png)

#  External measurement board structure

The external measurement board general structure is represented in Figure 2. It is composed of a power and a digital part separated by a galvanic isolation. The power part circuit is shown in Figure 3. It is composed of a power relay, 2 voltage measurements and 1 current measurement. The connectors A-B side is called "upstream" and connectors C-D side is called "downstream". The digital part is composed of a microcontroller (SPIN) that reads the measurements and sends to the controller and other modules via RS485 communication.

![Figure 2: External measurement board structure.](figures/image4.png)

![Figure 3: External measurement board power-side circuit.](figures/image5.png)

# KiCad schematic

## Power circuit

The power part of the external measurement board is composed of a solid state relay that connects upstream side to downstream side, connectors that connects to the converter circuit and voltage/current sensors.

**Questions left for discussion:**

- **For now I have kept the backplane structure as used for the modules for compatibility, but we also evoked the possibility of using banana connectors.**

![Figure 4: External measurement board power part.](figures/image6.png)

## Voltage and current sensors

The measurement solution is the same used in the new module board. The voltage raw measurement is acquired using a voltage divisor. The current raw measurement is acquired by an IC current sensor.

Then, both raw measurements are amplified and sent to the digital part of the board using a galvanic isolated amplifier.

Finally, the voltage and current measurements are treated by an analog front-end that is able to change the sensor gain, offset and resolution according to the application.

![Figure 5: Voltage divisor for raw voltage acquisition.](figures/image7.png)

![Figure 6: The raw measurement adaption stages for voltage (amplifier and analog front-end).](figures/image8.png)

![Figure 7: The current raw measurement with IC current sensor and amplification.](figures/image9.png)

![Figure 8: The current analog front-end.](figures/image10.png)

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

- **A adaptation to communicate with previous board versions is then necessary.**

![Figure 10: RJ45 terminals pinout.](figures/image12.png)

![Figure 11: RS485 communication schematic.](figures/image13.png)

![Figure 12: Analog communication schematic.](figures/image14.png)

![Figure 13: CAN communication schematic.](figures/image15.png)

## Auxiliary circuits supply

We adapted the auxiliary circuits supply feed generation of the microinverter project <https://github.com/owntech-foundation/micro-inverter>. The auxiliary circuits are fed by 5 VDGND, 5 VPUPGND and 5VPDOWNGND derived by the 12 VDGND input. The 12 VDGND input must be supplied by an external DC source.

The TPS7420 linear voltage regulator is used on the output of all +5V feeds to filter signal noises and keep the output on the target 5 V voltage.

![Figure 14: Auxiliary supply for PUPGND referenced components.](figures/image16.png)

![Figure 15: Auxiliary supply for PDOWNGND referenced components.](figures/image17.png)

![Figure 16: Auxiliary supply for DGND referenced components.](figures/image18.png)

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

![Figure 27: Placement of the voltage measurement circuit referenced to PUPGND in the layout.](figures/image29.png)

The analog-front end gets this digital output from the amplifier and adapts it according to user set gain, offset and resolution. The placement of the analog front-end for the 2 voltage measurements and current measurement was kept near the top part of the board as in the module boards developed by WP4, shown in Figure 28.

![Figure 28: Placement of the analog front-end for voltage and current measurements in the layout.](figures/image28.png)

## Current measurement circuit

The ACS724 isolated current sensor is used to acquire the raw current measurement passing from A to B connectors of the board. There is no recommended layout position in its datasheet, so the components where placed as close as possible to the pins. To have a smaller current path, the current sensor was placed the closest as possible to the relay I+ pin and VDOWN connector.

Similarly to the voltage measurement, the AMC1311x isolated amplifier is used to isolate the raw measurement of the power side from the digital measurement that is used by the MCU. In its datasheet, its recommended positioning with respect to the voltage divisor, input RC filter and decoupling capacitors is shown in Figure 26.

With the guidance of these datasheets indications, the placement of the current measurement circuit was implemented as shown in Figure 29.

![Figure 29: Placement of the current measurement circuit referenced to PDOWNGND in the layout.](figures/image30.png)

# Wiring

Wiring is still to be done.
