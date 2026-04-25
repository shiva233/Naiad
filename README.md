# 🔱 Naiad 💧
A USB-C rechargeable keychain (that hangs on your water bottle) that flashes to remind you to drink water if it hasn’t detected a sip in X minutes!

| <img width="415" alt="image" src="https://github.com/user-attachments/assets/a86b95ae-167f-4a80-a072-3f6d19545fa1" /> | <img width="352" alt="image" src="https://github.com/user-attachments/assets/b2b7446e-2845-4d4a-9dd9-a4a8c6aa4a1e" /> |
| :---: | :---: |

### What is Naiad? :0

Naiad is basically a small USB-C rechargeable keychain type thing that hangs off my HUGEE water bottle and will flash to remind me to drink water when it hasn't detected a sip every X amount of minutes (in my case I chose 15 minutes). I made it because I have a really bad habit of going the entire day without drinking much water, and thought this was a fun way to solve it!

<img width="172" height="274" alt="image" src="https://github.com/user-attachments/assets/8d40832a-5f8d-4326-9538-367c742fabbb" />

*(The big water bottle in question)*

### Technical Specs 🛠️

* **The MCU:** STM32U073K8U6 (Ultra-Low Power ARM Cortex-M0+)
* **The Sensor:** BMA400 3-axis Accelerometer (Super low power during sleep—less than 1 µA! :D)
* **Power:** 3.7V LIR2032 Rechargeable Coin Cell via USB-C
* **Charging & LDO:** MCP73831 Charger IC with an STLQ020C33R LDO
* **Battery Monitoring:** XC6135 Voltage Supervisor
* **Stackup:** 4-Layer Custom PCB (Signal - GND - VCC - Signal) to keep the sensor data super clean!

### Engineering Stuff ✨

Since this has to run off a tiny **40mAh** LIR2032 coin cell for a long time, power efficiency was the biggest goal! Here are a couple of my favorite parts of the hardware design:

**1. Obsessive Part Selection for Ultra-Low Power**
To make sure this thing could actually survive on a tiny coin cell for months, I had to be super picky about every single component on the board! It's not just about writing efficient firmware; the hardware itself needs to sip power:
* **The Brain:** I went with the STM32U073K8U6 because it's an ARM Cortex-M0+ built specifically for ultra-low power applications, drawing as little as **~1.5 µA** in its deep Stop modes. 
* **The Heart (Power Regulation):** A standard voltage regulator would waste too much battery just by existing, so I chose the STLQ020C33R LDO, which has an incredibly low quiescent current of just **300 nA (0.3 µA)**.
* **Battery Monitoring:** Even the voltage supervisor (the XC6135) was chosen specifically to monitor the battery level without constantly draining it, drawing **44 nA**. Every single nano-amp was accounted for! :D

**2. The BMA400 Accelerometer & Interrupts**
Instead of having the microcontroller constantly wake up to ask the accelerometer "did we move?" (which wastes a ton of battery), I used the BMA400's internal hardware interrupt engine! 

The sensor stays in a super low-power state (consuming only **800 nA**) and monitors for a specific "tilt" or "sip" gesture all on its own using its Auto-Wakeup feature. When it finally detects a sip, it triggers a hardware interrupt pin to wake up the STM32U0. 

There are a couple of other parts that were also chosenn specfically for their low power applications so overall the system's total quiescent current is safely **under 5µA**! 

**3. Seamless Power-Path Switching**
I wanted this to be as easy to use as possible—no annoying manual power switches needed to charge it! I designed an automatic power-path switching circuit using a P-FET (SSM3J64CTC) and a Schottky diode (BAS100AS). 

When you plug in a USB-C cable, the **5V** rail pulls the P-FET gate high, which instantly disconnects the battery from the main circuit. This lets the USB power the system *and* the battery charger (MCP73831) at the same time. When you unplug it, it seamlessly swaps back to battery power. Because the P-FET has an ultra-low on-resistance, the voltage drop across it is **less than 1mV** at these low currents, meaning the keychain never resets. It's totally plug-and-play! >:D

### Schematic

<img width="7017" height="4962" alt="Naiad Schematic PNG" src="https://github.com/user-attachments/assets/619a8f5d-2329-4ef2-a5e0-c760d8499404" />

### CAD & Enclosure 📦 (In Progress!)

I am currently in the middle of designing the 3D-printed enclosure for this right now, so check back soon for the final STEP files! :D

### Concept Sketches (Bad Handwriting be warned D:)

<img width="561" height="756" alt="image" src="https://github.com/user-attachments/assets/b8fef642-0c19-4d3f-a7d3-a103561dd713" />
