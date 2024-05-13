+++
title = 'Properly replace Cisco N5K fan modules by Noctua'
date = 2024-05-13T15:04:31+02:00
draft = true
categories = ['Cisco', 'Reverse Engineering', 'Cooling']
+++

> **DISCLAIMER**: The procedures in this document are provided as is, without warranty of any kind. Use of these procedures is at your own risk. We are not liable for any damages or losses resulting from the implementation of these guidelines.

## This project on paper

### Background
The Cisco Nexus 5000 (N5K) switches are integral to our network infrastructure but have presented challenges due to the high noise levels of their fan modules. This noise issue has made it impractical to integrate these switches into workplace environments where noise pollution must be kept to a minimum. As a result, there is a pressing need to address this to maintain both network performance and a conducive work atmosphere.

### Issues
1. **Proprietary Hardware:** Since Cisco is quite closed and oriented towards proprietary solutions, we need to reverse-engineer every part of this module. Fortunately, the vendor of the fans in our N5548P-FAN module appears to be more communicative about their hardware.
2. **Air Flow:** We must ensure sufficient airflow inside the switch to prevent any damage due to overheating.
3. **Automatic Failure Prevention**: This switch is protected against fan module removal and automatically shutdown after 60 seconds without response of the module.

### Solution
Replace the existing fans with 2x2 [Noctua (NF-A4x20)](https://noctua.at/fr/nf-a4x20-pwm) and create an artificial module response to prevent the automatic shutdown procedure.

## Reverse engineering

### About the fans

Each module contain two block of fans from Delta and every block is made out of a "inlet" fan and a "outlet" fan.

Here is more information about the block of fans:
- Vendor: [Delta - Fans and Thermal Management Products](https://www.delta-fan.com/)
- Model: GFB0412ES-E (datasheet is available on the vendor website)
- PWM: Yes
- Speed Measurement (RPM): Yes _(unfortunately for us...!)_

### Block Wiring
- GRAY == (-) OUTLET FAN
- BLACK == (-) INLET FAN
- RED == (+) INLET FAN
- ORANGE == (+) OUTLET FAN
- BLUE == (PWM) INLET FAN
- GREEN == (PWM) OUTLET FAN
- YELLOW == (TACH) INLET FAN TACH SIGNA (RPM)
- WHITE == (TACH) OUTLET FAN TACH SIGNAL (RPM)

During the analysis of the Cisco connector, we can clearly see that both wires of the block fan's speed measurement (yellow and white) are connected together. This means the signal is multiplexed and needs to be identified for future purposes.

> **Unfortunately, the previous step requires an oscilloscope, which I currently don't have. So, the project is suspended for the moment!**