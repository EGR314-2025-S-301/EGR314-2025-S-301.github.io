---
title: Block Diagram, Process Diagram, and Message Structure
tags:
- egr314
- team301
---

# Block Diagram, Process Diagram, and Message Structure

## Team Block Diagram

This image shows how our individual systems would connect to eachother to send messages over UART. As this is only meant to show the daisy-chain connection, the detainls of each system have been left out. For more detailed versions of the individual systems, check out our individual githubs! <br>

![EGR_314_Team_Block_Diaram-Page-1 drawio (1)](https://github.com/user-attachments/assets/33fdedd6-2b3a-41a9-8519-0ec4a5719b93)

**Individual Block Diagrams:**

- [Xander's Human Machine Interface](https://xanderheafey.github.io/Block-Diagram/)
- [Sara's Motor Subsystem](https://sarabohart.github.io/blockdiagram/)
- [Julia's Distance Sensor](https://juliasmith141414.github.io/blockdiagram/)
- [Ella's MQTT Communication](https://starfruwuit.github.io/egr314report/01BlockDiagram/)

### Justification of Block Diagram Structure

This Order was ideal because of who need what information more. Julia's system does not rely on any input from anyone, it simply outputs data and a judgement of if it is safe to opporate the motor. As suce, her system is at the front of our daisy chain. While the chain is a loop, thinking of it as a line makes it easier to understand who goes where. Next is Xander's system, because his OLED needs to output realtime distance data from Julia's sensor. If this data gets corrupted, his graph becomes inaccurate, so it is best to put their systems right next to eachother. Sara's motor driver needs information from both the distance sensor and the HMI, so this system is the next in the chain. It directly receives a planet value from the HMI, and the HMI passes along a 1 or 0 from the distance sensor. Both messages control the speed of the motor, which is shared with the MQTT server. The safety condition is also sent to the server. Ella's MQTT system does not have any critical information to send to any other system, so this system is the last in the daisy chain. This sequence puts those who rely most heavily on eachothers information closest together, optimising the efficiency of the communucation structure.

---

## How the Block Diagram Meets Product Requirements

The physical and logical structure supports:
- **User safety**: by prioritizing the distance sensor data and enforcing its position in the chain.
- **Low latency**: by placing interdependent systems next to each other.
- **Debuggability**: each module can operate independently, easing troubleshooting.
- **Expandability**: the daisy chain allows easy addition or removal of subsystems.

---

## Communication Process Diagram

This process diagram shows how the messages will be transfered over the daisy chain. 

![301 Sequence Diagram drawio (1)](https://github.com/user-attachments/assets/35a8e47c-466e-43e9-9cf5-69d27b3ec3d4)

---

## Communication Sequence Function Justification

The communication sequence satisfies the system's real-time and safety-critical constraints. Messages propagate from upstream to downstream, ensuring the motor does not operate unless a safety signal is received. The message format allows each subsystem to parse only the information relevant to them, preventing unnecessary delays or overloading the serial bus.

This message structure ensures timely response from the motor driver; prioritizing user safety overall other functions.  

---

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

---

### Why This Message Format?

- `AZ` and `YB` act as **start/end delimiters** to ensure correct parsing.
- Fixed-length messages simplify buffer management.
- Character-based IDs avoid collisions and ease message routing.
- System design uses **one UART bus**, so parsing and reliability are critical.

---

### Sample Message Example

**AZHB15YB**

- `AZ`: Start of message  
- `H`: Sent by Xander  
- `B`: Sent to Sara  
- `1`: Motor direction = forward  
- `5`: Motor speed = Jupiter gravity level  
- `YB`: End of message  

---

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

## Top 5 Biggest Changes to the Software Design

1. Splitting the Motor Subsystem into Two PICs  
   Originally, a single PIC handled both UART parsing and SPI motor control. As complexity increased, messages interfered with SPI performance. The system was split: one PIC handles message parsing and logic, the second controls the motor based on logic pin inputs.

2. Logic Gate Between Motor PICs  
   To support the new dual-PIC setup, a logic-based communication system using 4 GPIO lines was implemented. One line (B0) communicates safety. The remaining 3 (B1–B3) encode the selected planet using a truth table. This allowed safe and reliable signaling between the PICs without blocking UART.

3. Added Message Framing (`AZ...YB`)  
   Framing messages with start (`AZ`) and end (`YB`) characters significantly improved message parsing reliability and allowed systems to easily resynchronize if a byte was dropped.

4. Error Handling Improvements  
   Systems now ignore malformed or misaddressed messages, rather than crashing. This change made debugging faster and communication more robust under load.

5. Real-time OLED Feedback for Debugging  
   Xander’s OLED system was originally for user display only, but was updated to show real-time system status (distance, motor state) for easier debugging. This added a valuable verification layer for the entire chain.

---
