---
title: Reflection
tags:
- tag1
- tag2
---

## Version 2.0
In the next iteration of this project, we would dedicate more time building the housing for the final product. Our team was very focussed on getting all of the code to work, which paid off in a well functioning final project, but we should have delegated the building of the final housing to those who had finished their code sooner than the rest of the group. 
Additionally, there would be a redesign of the motor system, as it grew to cover 3 PCBs. These boards would be consolidated into one final board, eliminating the possibility of a fly wire coming loose and ruining the whole system. 
Furthermore, the final design should use less headers, as we do not expect the museum staff to need to wire any new components into the final product. The power selection headers should be replaced with the appropriate fuses to protect the systems from serges, which would be more important to the end users than the modularity that the headers granted. 
If possible, the whole project should be condensed into less PCBs to save on cost and space. This would also allow the 8 pin connections to be hard wired, eliminating the possibility of plugging the wires in wrong, or the connectors rotating or bending resulting in a short between the unregulated power and the UART data pins. As this redesign would be very extreme, it may need to wait for a version 3.0. At a minimum, the order of the headers should be changed to keep the UART pin as far from the unregulated power as possible. This will help to protect the microcontrollers from the higher voltage, increasing the longevity and stability of the product.
