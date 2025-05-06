---
title: Block Diagram, Process Diagram, and Message Structure
tags:
- tag1
- tag2
---
## Team Block Diagram
This image shows how our individual systems would connect to eachother to send messages over UART. As this is only meant to show the daisy-chain connection, the detainls of each system have been left out. For more detailed versions of the individual systems, check out our individual githubs! <br>
![EGR_314_Team_Block_Diaram-Page-1 drawio (1)](https://github.com/user-attachments/assets/33fdedd6-2b3a-41a9-8519-0ec4a5719b93)
<br>
[Xander's Human Machine Interface](https://xanderheafey.github.io/Block-Diagram/)<br>
[Sara's Motor](https://sarabohart.github.io/blockdiagram/)<br>
[Julia's Sensor](https://juliasmith141414.github.io/blockdiagram/)<br>
[Ella's Bidirectional Internet Communication](https://starfruwuit.github.io/egr314report/01BlockDiagram/)
<br>
### Justification of Block Diagram Structure
This Order was ideal because of who need what information more. Julia's system does not rely on any input from anyone, it simply outputs data and a judgement of if it is safe to opporate the motor. As suce, her system is at the front of our daisy chain. While the chain is a loop, thinking of it as a line makes it easier to understand who goes where. Next is Xander's system, because his OLED needs to output realtime distance data from Julia's sensor. If this data gets corrupted, his graph becomes inaccurate, so it is best to put their systems right next to eachother. Sara's motor driver needs information from both the distance sensor and the HMI, so this system is the next in the chain. It directly receives a planet value from the HMI, and the HMI passes along a 1 or 0 from the distance sensor. Both messages control the speed of the motor, which is shared with the MQTT server. The safety condition is also sent to the server. Ella's MQTT system does not have any critical information to send to any other system, so this system is the last in the daisy chain. This sequence puts those who rely most heavily on eachothers information closest together, optimising the efficiency of the communucation structure.
## Process Diagram
<br> This process diagram shows how the messages will be transfered over the daisy chain. 
![301 Sequence Diagram drawio (1)](https://github.com/user-attachments/assets/35a8e47c-466e-43e9-9cf5-69d27b3ec3d4)

## Message Structure
This table details the messages that will be sent between the boards. The identifiers at the begining of the messages show who is sending the message to whom. The messages are labled with English identifiers for ease of reading. <br>

|Description	|Byte 1-2 (char)	|Byte 3 (char)	|Byte 4 (char)	|M	|Byte 63 (char)	|Byte 64 (char)|
|-------------|---------------------|-----------------|-------------------|---|-------------------|------------------|
|Desired speed	|AZ(char)|H(char)	|B(char)	|Change Speed(char)	|Y (char)	|B (char)|
|User Safe?	|AZ(char)	|S(char)	|B(char)	|Check Distance(char)	|Y (char)	|B (char)|
|Direct Drive	|AZ(char)	|H(char)	|B(char)	|Change Direction(char)	|Y (char)	|B (char)|
|Motor Speed	|AZ(char)	|B(char)	|G(char)	|Speed(int)	|Y (char)	|B (char)|
|Distanc Data |AZ(char) |S(char)  |H(char)  |Distance(int) |Y(char) |B(char)|

### Key
Systems	| IDs
--------|------------
Xander	|H(char)
Sara	  |B(char)
Julia	  |S(char)
Ella	  |G(char)

<br>The IDs are assigned based on the sender and receiver's last names, as X would interfear with the brodcast character defined by the course.<br>
### Sample String
For example, the string sent from Xander to Sara concerning the direction and speed of the motor would be written as follows:<br>
**AZHB05YB** <br>
AZ identifies the start of a string<br>
H means that Xander's system is sending the message<br>
B means that the message is meant to be received by Sara's system<br>
1 indicates the direction Sara's motor should spin <br>
5 indicates the speed should be representitive of Jupiter's gravity <br>
YB indicates the end of the string

## Biggest Changes to the Software
Since the software proposal, there have been many changes to the software. The following are the most notable updates since the initial software proposal. Some of these changes have been noted and absorbed into the report above, but they are updates nonetheless. <br>
1. **Splitting the motor system across two PICs.:** Due to the intensity of the software required for the message handling and the SPI control of the motor driver, the code could not run fully on one PIC. When the SPI code was added to the API project, the API stopped processing messages, and when the API code was added to the SPI project, the SPI stopped controlling the motor. Despite the added cost, the most efficient solution was to program two separate PICs and have them communicate with each other. One would analyse and handle the messages from the UART daisy chain, while the other would take that information and determine how to control the motor driver
2. **Inclusion of the motor driver’s logic gate.:** In order to use two PICs in one system, there needed to be a way for them to communicate with each other without interfering with the other systems. To do this, a 4 pin logic system was built between the PICS to determine what was selected. The first pin, B0 on both PICS, was high when the distance sensor indicated that it was safe to move the motor. Without this input, the motor would not spin. This also meant if this pin got disconnected, the motor would turn off, making it safer to reconnect the pins. The next three pins, B1, B2, and B3, turn on and off in specific patterns to indicate which planet is selected. All three pins being low indicates pluto, which has a relative RPM of 0, making it safer to reconnect any disconnected wires. For testing and demonstration purposes, LEDs were added on each logic line to indicate when they were high and low. The complete logic table is as follows <br>

  |Setting|B0|B1|B2|B3|
  |-------|---|---|---|---|
  |Off for safety|low|either|either|either|
  |Mercury|either|high|high|high|
  |Venus|either|high|low|low|
  |Moon|either|low|high|low|
  |Mars|either|low|low|high|
  |Jupiter|either|high|high|low|
  |Saturn|either|low|high|high|
  |Neptune|either|high|low|high|
  |Pluto|either|low|low|low|

<br>
