# Smart Voice-Controlled Home Automation with Energy Monitoring

##  Project Overview
This project demonstrates an **IoT-based Smart Home Automation System** using **NodeMCU (ESP8266)**.  
The system allows users to control household appliances remotely using **voice commands or a mobile application**.  
It also provides **energy monitoring and estimated electricity bill calculation**, displayed on an **LCD screen**.

The project integrates **NodeMCU, relay modules, bulbs, LCD display, and push buttons** to create a functional smart home prototype.

---

##  Objectives
- To control home appliances remotely using IoT technology.
- To integrate **voice control using Google Assistant or Alexa**.
- To allow **manual switching using push buttons**.
- To display appliance status and energy usage on an **LCD display**.
- To estimate electricity consumption and bill.

---

##  Components Used

| Component | Quantity |
|----------|---------|
| NodeMCU (ESP8266) | 1 |
| 3-Channel Relay Module | 1 |
| 16x2 LCD Display (I2C) | 1 |
| Push Buttons | 2 |
| Bulbs (Load) | 3 |
| Jumper Wires | As required |
| Power Supply (5V/USB) | 1 |

---

##  Working Principle
1. The **NodeMCU (ESP8266)** connects to Wi-Fi and communicates with the **Sinric Pro cloud platform**.
2. The user sends a **voice command through Google Assistant or Alexa**.
3. The command is processed by the cloud server and sent to the **NodeMCU**.
4. The NodeMCU activates the **relay module** to control connected appliances.
5. Each relay controls one bulb independently.
6. The system estimates **energy consumption and electricity bill**.
7. The **LCD display** shows appliance status and energy usage.
8. **Push buttons** provide manual control if internet connectivity is unavailable.

---

## 🔌 Block Diagram

Google Assistant / Alexa
↓
Internet
↓
Sinric Pro Cloud
↓
WiFi
↓
NodeMCU ESP8266
↓
Relay Module
↓ ↓ ↓
Bulb1 Bulb2 Bulb3

Additional Connections:
NodeMCU → LCD Display
NodeMCU → Push Buttons


---

##  Energy Calculation
Energy consumption is calculated using the formula:

Energy (E) = Power (P) × Time (t)


Where:
- **E** = Energy (kWh)
- **P** = Power of appliance (Watts)
- **t** = Time of usage (hours)

Electricity bill estimation:

Cost = Energy × Tariff Rate


---

##  Output
The system provides:
- **Voice-controlled appliance switching**
- **Remote control via mobile application**
- **LCD display showing appliance status**
- **Energy consumption monitoring**
- **Estimated electricity bill**

---

##  Prototype
The prototype consists of:
- NodeMCU microcontroller
- Relay module controlling three bulbs
- LCD display
- Push buttons for manual control

---

##  Learning Outcomes
- Learned **IoT device programming using NodeMCU (ESP8266)**.
- Understood integration of **cloud platforms like Sinric Pro**.
- Developed skills in **interfacing relays, LCDs, and push buttons**.
- Gained knowledge of **voice-controlled automation systems**.
- Learned **basic energy monitoring and bill estimation techniques**.

---

##  Limitations
- The system currently supports only **three appliances**.
- Energy monitoring is **basic and approximate**.
- Remote operation requires a **stable internet connection**.

---

##  Future Enhancements
- Support for more appliances.
- Advanced energy analytics with historical data.
- Mobile application with improved UI.
- Integration of additional sensors such as **temperature and humidity sensors**.

---

##  Safety Precautions
- Ensure proper insulation for all electrical connections.
- Avoid direct contact with live wires.
- Use proper circuit protection and fuses.
- Disconnect power before modifying the circuit.

---

##  Applications
- Smart homes
- Office automation
- Energy management systems
- Elderly assistance systems
- Educational IoT demonstrations

---

##  License
This project is created for **educational and academic purposes**.
