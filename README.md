Smart Baby Room Monitor (IoT-Based Environmental Safety System)
An IoT-based smart home automation solution engineered to continuously track, assess, and report nursery environments. This project monitors ambient temperature and relative humidity using an ESP32 microcontroller and a DHT sensor, generating instant localized sensory alarms while feeding a continuous telemetry log stream to the cloud.

🔗 Live Project Links
Wokwi Interactive Simulation: Launch Simulation Platform                                                             
ThingSpeak Cloud Analytics: View Public Live Channel Dashboard
📺 Project Media Demonstrations
System Architecture Snapshot
Hardware Connections Circuit Diagram

system working explaination
 baby_room_monitoring_system.2.mp4 

🛠️ System Architecture & Hardware Pinout
The hardware logic layout is fully simulated inside the Wokwi Virtual Environment using the following connection schema:

Input/Output Component	Hardware Variant	ESP32 GPIO Microprocessor Pin	Functional Role
Environmental Sensor	DHT22 / DHT11	GPIO 15	Digital Data Bus
Visual Alert Indicator	Red LED	GPIO 12	Unsafe Zone Status
Status Indicator	Green LED	GPIO 14	Safe Zone Status
Acoustic Warning Alarm	Piezo Buzzer	GPIO 13	Frequency Driven Pulse (1kHz)
🧠 Core Operational Logic
1. Environmental Threshold Guardrails
The software actively parses incoming float arrays against certified comfortable baby environment ranges:

Optimal Temperature Boundaries: 
20.0
∘
C
≤
Temperature
≤
26.0
∘
C
Optimal Relative Humidity Boundaries: 
40.0
2. Dual-Layer Fail-Safe Handling
Local Level: Processing relies on active evaluation scripts. If boundaries fail, loops toggle status LEDs instantly and pass square wave pulses via tone(BUZZER, 1000) to resolve static digital signal issues common to simulations.
Cloud Level: Instead of simple delays, data transfers evaluate time differentials through an asynchronous millis() framework. This isolates cloud updates (required every 15 seconds by ThingSpeak's API) from internal system checking frequencies (executing seamlessly every 2 seconds).
📊 Cloud Telemetry Layout (ThingSpeak)
Data structures map directly into individual data blocks inside your cloud instance:

Field 1: Ambient Real-Time Temperature (
∘
C
)
Field 2: Relative Air Humidity (
0
--
100
)
Field 3: Cumulative Safety Violation Incident Counter
🚀 How to Run the Project Local Implementation
Clone this repository structure:
   git clone https://github.com/tech-by-niteshh/baby_room_monitoring_system.git
