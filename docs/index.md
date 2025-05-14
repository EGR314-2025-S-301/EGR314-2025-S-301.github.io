---
title: Home
tags:
- tag1
- tag2
---

## STEMteresting
### Team 301
### Ella Greetis, Sara Bohart, Julia Smith, Xander Heafey
1/16/25 <br>
Spring 2025 <br>
Arizona State University <br>
EGR 314, Prof. Nichols <br>

[Team Organization](team-organization.md) <br>
[Ideation and Concept Generation](ideation-and-concept-generation.md) <br>
[Block Diagram, Process Diagram, and Message Structure](Block-Diagram-Process-Diagram-and-Message-Structure.md) <br>
[API](https://egr314-2025-s-301.github.io/API/)<br>
[Innovation Showcase Poster](https://egr314-2025-s-301.github.io/Innovation%20Showcase%20Poster/)<br>
[Reflection](https://egr314-2025-s-301.github.io/Reflection/)<br>
[Resources](https://egr314-2025-s-301.github.io/Resources/)<br>

### Individual GitHub Links
[Ella Greetis](https://starfruwuit.github.io/egr314report/00mainPage/) <br>
[Sara Bohart](https://sarabohart.github.io/) <br>
[Julia Smith](https://juliasmith141414.github.io/juliasmith-stemteresting/) <br>
[Xander Heafey](https://xanderheafey.github.io/) <br>

## Our Project
Our project is a planetary centrifuge that shows the relative differences in gravity between different celestial bodies in our solar system. The mechanism works by changing the speed of a motor to match the calculated rpm that will cause the centrifugal force to match the gravity of the selected celestial body. The online calculator used to determine the rpm can be found [here](https://www.sigmaaldrich.com/US/en/support/calculators-and-apps/g-force-calculator?srsltid=AfmBOop8NaSsvon0b_BEHic9ZCmkiujIRdpJg4A7WGM2Ohh8W5gX9p2s).<br>
The motor is controlled using SPI. It turns on and off at the appropriate intervals to create the duty cycle that results in the proper rpm. The time the motor is on for is calculate by the length of the full duty cycle times the desired rpm over the max rpm of the motor. The time the motor is off is calculated as the full duty cycle minus the time the motor is on. <br>
The planet it selected through the HMI system. A list of planets and celestial bodies is shown on an OLED screen. Users can use a joystick to select their planet or celestial body. This information is communicated with the motor system over UART and a fun fact about the planet is displayed on the screen. The OLED can also display real time distance data from the distance sensor system, which it is receiving over UART, and directly drive the motor from the direct drive menu. <br>
The distance sensor is a safety feature that checks to see if someone is too close to the motor for it to safely operate. If someone is in range, the sensor tells the motor not to spin. This also happens over UART. <br>
The MQTT system allows the project to have bidirectional internet communication. The speed setting and the safety data are written to the server using an ESP-32, which receives this information through the UART daisy chain. Users can also write a review on the MQTT Server, resulting in an LED flashing 5 times to indicate that the message was received. <br>
Together, these system create a safe and interactive exhibit that introduces users to the relative differences in gravity on different celestial bodies in our solar system. Our hope is to encourage their curiosity and interest in space and physics as a whole. <br>


## Final PCB Designs
### Julia
![Julia PCB Front](https://github.com/user-attachments/assets/d7a70ee6-d0e1-49a0-8e48-700dd82a195a)
![Julia PCB Back](https://github.com/user-attachments/assets/8368e205-c7c7-4621-8deb-24f211963443)
### Xander
![Xander PCB Front](https://github.com/user-attachments/assets/0183b604-de67-4874-8c75-baa1924f3bad)
![Xander PCB Back](https://github.com/user-attachments/assets/f01a29be-8fa6-4a80-9d81-2a5dbc5d9bac)
### Sara
![Sara PCB Front](https://github.com/user-attachments/assets/200b6da4-5e93-45ec-8db5-4831729a0f27)
![Sara PCB Back](https://github.com/user-attachments/assets/161067b8-283f-4fd2-825e-0830a58d36e6)
### Ella
![Ella PCB Front](https://github.com/user-attachments/assets/51231579-f41f-4059-a392-ebebe0b104b2)
![Ella PCB Back](https://github.com/user-attachments/assets/1d4a6af9-5cdd-4e07-bfbd-889014880a10)
