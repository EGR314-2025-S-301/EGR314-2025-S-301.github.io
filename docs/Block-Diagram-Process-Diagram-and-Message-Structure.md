---
title: Block Diagram, Process Diagram, and Message Structure
tags:
- egr314
- team301
---

# Block Diagram, Process Diagram, and Message Structure

## Team Block Diagram

This image shows how our individual systems connect to one another to send messages over UART. Because this diagram is intended to demonstrate the daisy-chain communication structure, individual system details have been excluded. For subsystem-specific designs, please view our individual GitHub pages.

![EGR_314_Team_Block_Diaram-Page-1 drawio (1)](https://github.com/user-attachments/assets/33fdedd6-2b3a-41a9-8519-0ec4a5719b93)

**Individual Block Diagrams:**

- [Xander's Human Machine Interface](https://xanderheafey.github.io/Block-Diagram/)
- [Sara's Motor Subsystem](https://sarabohart.github.io/blockdiagram/)
- [Julia's Distance Sensor](https://juliasmith141414.github.io/blockdiagram/)
- [Ella's MQTT Communication](https://starfruwuit.github.io/egr314report/01BlockDiagram/)

### Justification of Block Diagram Structure

This communication order was chosen based on system dependencies and communication needs. Julia’s distance sensor outputs a binary safety flag and distance data, but requires no input, so it is placed at the start of the chain. Xander’s OLED must display this real-time data with minimal latency, so his system follows Julia’s. Sara’s motor driver uses both the planet selection from Xander and the safety status from Julia, placing her system third. Ella’s MQTT system transmits state information externally and doesn’t control internal systems, so it is logically last.

This organization prioritizes **communication efficiency** and **safety**. Systems that rely on each other’s data are adjacent, minimizing the chance of message delay or corruption.

---

## How the Block Diagram Meets Product Requirements

The physical and logical structure supports:
- **User safety**: by prioritizing the distance sensor data and enforcing its position in the chain.
- **Low latency**: by placing interdependent systems next to each other.
- **Debuggability**: each module can operate independently, easing troubleshooting.
- **Expandability**: the daisy chain allows easy addition or removal of subsystems.

---

## Communication Process Diagram

This process diagram visualizes how messages move between the systems.

![301 Sequence Diagram drawio (1)](https://github.com/user-attachments/assets/35a8e47c-466e-43e9-9cf5-69d27b3ec3d4)

---

## Communication Sequence Function Justification

The communication sequence satisfies the system's real-time and safety-critical constraints. Messages propagate from upstream to downstream, ensuring the motor does not operate unless a safety signal is received. The message format allows each subsystem to parse only the information relevant to them, preventing unnecessary delays.

This structure ensures:
- **Timely response to unsafe conditions**
- **Correct interpretation of multi-parameter commands (planet, direction, safety)**
- **Modular expansion or substitution of subsystems**

---

## Message Structure

The following table defines all expected messages between subsystems. Messages are 64 characters long, and framed with identifiers for the start and end of a message.

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

## Top 5 Biggest Changes to the Software Design

1. **Splitting the Motor Subsystem into Two PICs**  
   Originally, a single PIC handled both UART parsing and SPI motor control. As complexity increased, messages interfered with SPI performance. The system was split: one PIC handles message parsing and logic, the second controls the motor based on logic pin inputs.

2. **Logic Gate Between Motor PICs**  
   To support the new dual-PIC setup, a logic-based communication system using 4 GPIO lines was implemented. One line (B0) communicates safety. The remaining 3 (B1–B3) encode the selected planet using a truth table. This allowed safe and reliable signaling between the PICs without blocking UART.

3. **Added Message Framing (`AZ...YB`)**  
   Framing messages with start (`AZ`) and end (`YB`) characters significantly improved message parsing reliability and allowed systems to easily resynchronize if a byte was dropped.

4. **Error Handling Improvements**  
   Systems now ignore malformed or misaddressed messages, rather than crashing. This change made debugging faster and communication more robust under load.

5. **Real-time OLED Feedback for Debugging**  
   Xander’s OLED system was originally for user display only, but was updated to show real-time system status (distance, motor state) for easier debugging. This added a valuable verification layer for the entire chain.

---

## Summary

This final communication and software structure prioritizes **user safety**, **debugging**, and **expandability**. The block diagram reflects clean modularity, and the message structure allows for efficient and reliable parsing even under load.

Our final system not only fulfills the original design goals but also adapts to evolving hardware needs, such as multi-PIC coordination and real-time feedback.

