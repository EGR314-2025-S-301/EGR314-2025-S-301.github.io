---
title: API
tags:
- tag1
- tag2
---
This page outlines the massages out boards are sending and receiving from eachother.
## Standard Message structure
Byte 1|Byte 2|Byte 3|Byte 4|Byte 5|...|Byte 5+x|Byte 6+x|Byte 7+x|
------|------|------|------|------|------|---|------|------|
A|Z|Sender Initial|Receiver Initial|Data 1|...|Data x|Y|B|

 <br>
Where x is the number of data points sent in the message.<br>
All values for the bytes in the messages are characters unless explicitly specified for the data points.
<br>
Each initial is based on the team member's last name. This choice was made so the letter X could be left for global signals, as required by the project.<br>

## Team Initials
Member|Initial|
------|-------|
Sara|B|
Xander|H|
Julia|S|
Ella|G|

Each one of these initials are characters.

## Team Messages
The following tables show what messages each member is sending to each member. for easier navigation, use the side bar.
### Sara
A complete report of the messages Sara's system can handle can be found [here](https://sarabohart.github.io/api/).
#### Sara to Xander
Sara's system does not send any values to Xander's system. It only receives values from Xander's system.
#### Sara to Julia
Sara's system does not send any values to Julia's system. It only receives values from Julia's system.
#### Sara to Ella
Sara's system is communicating the current RPM of the motor as an intiger value to Ella's MQTT system so it can be displayed on the internet. This value can be between -200 and 200.
##### String format
Byte 1|Byte 2|Byte 3|Byte 4|Byte 5|Byte 7|Byte 8|
------|------|------|------|------|------|------|
A char)|Z (char)|B (char)|G (char)|rpm (int)|Y (char)|B (char)|

### Xander
A complete report of the messages Xander's system can handle can be found [here](-LINK TO XANDER'S API PAGE-).
#### Xander to Sara
#### Xander to Julia
#### Xander to Ella

### Julia
A complete report of the messages Julia's system can handle can be found [here](-LINK TO JULIA'S API PAGE-).
#### Julia to Sara
#### Julia to Xander
#### Julia to Ella

### Ella
A complete report of the messages Ella's system can handle can be found [here](https://starfruwuit.github.io/egr314report/05API/).
Ella's board is also capable of sending "feedback" messages from the MQTT server to any teammate.
#### Ella to Sara
Ella's subsystem does not send any messages to Sarah's subsystem.
#### Ella to Xander
Ella's subsystem does not send any messages to Xander's subsystem. 
#### Ella to Julia
Ella's subsystem does not send any messages to Julia's subsystem.
### Full API Table

Message Type|Message ID Type:|Xander Role: HMI ID: H|Julia Role: Sensor ID: S|Sarah Role: Motor ID: B|Ella Role: Web ID: G|
------|------|------|------|------|------|
Desired speed|H|S (Press Planet)|-|R (Change motor speed according to planet)|-|
User Safe?|S|-|S (User safe based off of distance)|R (Turn on or off Motor)|R (Publish user is safe)|
Direct Drive|H|S (Spin left or right)|-|R (motor spins left or right)|-|
Motor Speed|B|-|-|S (speed of motor)|R (publish motor speed to mqtt)|
Distance Data|S|R (Display Distance Data on Screen)|S (Send distance)|-|-|