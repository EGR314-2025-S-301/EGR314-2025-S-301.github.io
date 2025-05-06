---
title: Block Diagram, Process Diagram, and Message Structure
tags:
- egr314
- team301
---

# Block Diagram, Process Diagram, and Message Structure

## Team Block Diagram

This image shows how our individual systems would connect to eachother to send messages over UART. As this is only meant to show the daisy-chain connection, the details of each system have been left out. For more detailed versions of the individual systems, check out our individual githubs! <br>

![EGR_314_Team_Block_Diaram-Page-1 drawio (1)](https://github.com/user-attachments/assets/33fdedd6-2b3a-41a9-8519-0ec4a5719b93)

**Individual Block Diagrams:**

- [Xander's Human Machine Interface](https://xanderheafey.github.io/Block-Diagram/)
- [Sara's Motor Subsystem](https://sarabohart.github.io/blockdiagram/)
- [Julia's Distance Sensor](https://juliasmith141414.github.io/blockdiagram/)
- [Ella's MQTT Communication](https://starfruwuit.github.io/egr314report/01BlockDiagram/)

### Justification of Block Diagram Structure

This Order was ideal because of who need what information more. Julia's system does not rely on any input from anyone, it simply outputs data and a judgement of if it is safe to opporate the motor. As suce, her system is at the front of our daisy chain. While the chain is a loop, thinking of it as a line makes it easier to understand who goes where. Next is Xander's system, because his OLED needs to output realtime distance data from Julia's sensor. If this data gets corrupted, his graph becomes inaccurate, so it is best to put their systems right next to eachother. Sara's motor driver needs information from both the distance sensor and the HMI, so this system is the next in the chain. It directly receives a planet value from the HMI, and the HMI passes along a 1 or 0 from the distance sensor. Both messages control the speed of the motor, which is shared with the MQTT server. The safety condition is also sent to the server. Ella's MQTT system does not have any critical information to send to any other system, so this system is the last in the daisy chain. This sequence puts those who rely most heavily on eachothers information closest together, optimising the efficiency of the communucation structure.

## How the Block Diagram Meets Product Requirements
The way we structured the system helps with a few important things. First, putting the distance sensor at the front of the chain makes sure user safety is prioritized. We also kept systems that rely on each other close together to reduce delay and keep everything responsive. Each module can run on its own, which makes troubleshooting easier, and the daisy chain setup makes it simple to add or remove subsystems if we need to make changes later.

## Communication Process Diagram

This process diagram shows how the messages will be transfered over the daisy chain. 

![301 Sequence Diagram drawio (1)](https://github.com/user-attachments/assets/35a8e47c-466e-43e9-9cf5-69d27b3ec3d4)

## Communication Sequence Function Justification

The communication sequence satisfies the system's real-time and safety-critical constraints. Messages propagate from upstream to downstream, ensuring the motor does not operate unless a safety signal is received. The message format allows each subsystem to parse only the information relevant to them, preventing unnecessary delays or overloading the serial bus.

This message structure ensures timely response from the motor driver; prioritizing user safety overall other functions.  

## Message Structure

This table details the messages that will be sent between the boards. The identifiers at the begining of the messages show who is sending the message to whom. The messages are labled with English identifiers for ease of reading. <br>

| Description     | Byte 1-2 | Byte 3 | Byte 4 | Byte 5 (Data)       | Byte 63 | Byte 64 |
|----------------|----------|--------|--------|----------------------|---------|---------|
| Desired speed  | AZ       | H      | B      | Change Speed (char) | Y       | B       |
| User Safe?     | AZ       | S      | B      | Safety Flag (0/1)   | Y       | B       |
| Direct Drive   | AZ       | H      | B      | Direction (char)    | Y       | B       |
| Motor Speed    | AZ       | B      | G      | Speed (int)         | Y       | B       |
| Distance Data  | AZ       | S      | H      | Distance (int)      | Y       | B       |

### ID Legend

| Subsystem | ID (char) |
|-----------|-----------|
| Julia     | S         |
| Xander    | H         |
| Sara      | B         |
| Ella      | G         |

### Why This Message Format?
We chose this message format because it ensures correct parsing. Byte 1-2 ("AZ") identify the beggining of a message. Byte 3 identifies the sender. Byte 4 identifies the receiver. Byte 5-62 stores the message data. Byte 63-64 ("YB") identifies the end of the message.
This format allows individual subsystems to ignore messages not intendend for them which ensures there is no unecessary processesing/lag time. It also ensures corrupted messages can be identified and removed from the loop. 

### Sample Message Example
For example, the string sent from Xander to Sara concerning the direction and speed of the motor would be written as follows: <br>
**AZHB15YB**

- `AZ`: Start of message  
- `H`: Sent by Xander  
- `B`: Sent to Sara  
- `1`: Motor direction = forward  
- `5`: Motor speed = Jupiter gravity level  
- `YB`: End of message  

<br>The IDs are assigned based on the sender and receiver's last names, as X would interfear with the brodcast character defined by the course.<br>

## Biggest Changes to the Software
Since the software proposal, there have been many changes to the software. The following are the most notable updates since the initial software proposal. Some of these changes have been noted and absorbed into the report above, but they are updates nonetheless. <br>
1. **Splitting the motor system across two PICs.:** <br>
Due to the intensity of the software required for the message handling and the SPI control of the motor driver, the code could not run fully on one PIC. When the SPI code was added to the API project, the API stopped processing messages, and when the API code was added to the SPI project, the SPI stopped controlling the motor. Despite the added cost, the most efficient solution was to program two separate PICs and have them communicate with each other. One would analyse and handle the messages from the UART daisy chain, while the other would take that information and determine how to control the motor driver <br>
2. **Inclusion of the motor driver’s logic gate.:** <br>
In order to use two PICs in one system, there needed to be a way for them to communicate with each other without interfering with the other systems. To do this, a 4 pin logic system was built between the PICS to determine what was selected. The first pin, B0 on both PICS, was high when the distance sensor indicated that it was safe to move the motor. Without this input, the motor would not spin. This also meant if this pin got disconnected, the motor would turn off, making it safer to reconnect the pins. The next three pins, B1, B2, and B3, turn on and off in specific patterns to indicate which planet is selected. All three pins being low indicates pluto, which has a relative RPM of 0, making it safer to reconnect any disconnected wires. For testing and demonstration purposes, LEDs were added on each logic line to indicate when they were high and low. The complete logic table is as follows <br>

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
