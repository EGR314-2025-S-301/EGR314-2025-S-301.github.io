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
## Process Diagram
<br> This process diagram shows how the messages will be transfered over the daisy chain. 
![301 Sequence Diagram](Process%20v2-1.png)
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
