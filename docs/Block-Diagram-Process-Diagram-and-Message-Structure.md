---
title: Block Diagram, Process Diagram, and Message Structure
tags:
- tag1
- tag2
---
## Team Block Diagram
This image shows how our individual systems would connect to eachother to send messages over UART. As this is only meant to show the daisy-chain connection, the detainls of each system have been left out. For more detailed versions of the individual systems, check out our individual githubs! <br>
![EGR_314_Team_Block_Diaram-Page-1 drawio](https://github.com/user-attachments/assets/bdf843b4-6444-43c4-8d9b-3085cfe64fee) <br>
[Xander's Human Machine Interface](https://xanderheafey.github.io/Block-Diagram/)<br>
[Sara's Motor](https://sarabohart.github.io/blockdiagram/)<br>
[Julia's Sensor](https://juliasmith141414.github.io/blockdiagram/)<br>
[Ella's Bidirectional Internet Communication](https://starfruwuit.github.io/egr314report/01BlockDiagram/)
## Process Diagram
<br> This process diagram shows how the messages will be transfered over the daisy chain. 
![301 Sequence Diagram](https://github.com/user-attachments/assets/b03f7472-f672-4f33-be5f-4564422824ab) 
## Message Structure
This table details the messages that will be sent between the boards. The identifiers at the begining of the messages show who is sending the message to whom. The messages are labled with English identifiers for ease of reading. <br>

Message Type Byte 1-2 (char)	|Description	|Byte 1-2 (char)	|Byte 3 (char)	|Byte 4 (char)	|M	|Byte 63 (char)	|Byte 64 (char)|
----------------------------------|-------------|---------------------|-----------------|-------------------|---|-------------------|------------------|
1	|Desired speed	|0x01(char)|H(char)	|B(char)	|Change Speed(char)	|Y (char)	|B (char)|
2	|User Safe?	|0x02(char)	|B(char)	|S(char)	|Check Distance(unit8_t)	|Y (char)	|B (char)|
3	|Motor On	|0x03(char)	|S(char)	|B(char)	|Yes(char)	|Y (char)	|B (char)|
4	|Motor Off	|0x04(char)	|S(char)	|B(char)	|No(char)	|Y (char)	|B (char)|
5	|Direct Drive	|0x05(char)	|H(char)	|B(char)	|Change Direction(char)	|Y (char)	|B (char)|
6	|Motor Speed	|0x06(char)	|B(char)	|G(char)	|Speed(char)	|Y (char)	|B (char)|

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
