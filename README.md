# 🚰 Smart Water Tank Monitoring and Auto-Pump Control System 💧

An Arduino-based project that intelligently monitors water level and temperature in a water tank, automatically controls a pump, and alerts users via buzzer, LEDs, and LCD.

---

## 📑 Table of Contents

* [Overview](#overview)
* [Features](#features)
* [Hardware Components](#hardware-components)
* [Circuit Diagram](#circuit-diagram)
* [Screenshots to Add](#screenshots-to-add)
* [Source Code](#source-code)
* [Working Principle](#working-principle)
* [Tinkercad Simulation Link](#tinkercad-simulation-link)
* [Acknowledgements](#acknowledgements)

---

## 🧠 Overview

This project is designed to **monitor the water level** in a storage tank using an **ultrasonic sensor** and **temperature using LM35**. It shows the **volume of water (in liters)** and **temperature (in Celsius)** on an LCD screen and automatically turns **pump ON or OFF** based on the level of water. It also gives **visual (RGB LED)** and **audio (buzzer)** alerts.

---

## ✨ Features

* Automatic water pump control 💡
* Real-time water level and temperature display 📟
* Audible buzzer alerts 🔊
* Color-coded LED indicators 🔴🔵🟢
* Volume calculation in liters 🧪
* Cost-effective & easy to build 💰

---

## 🔩 Hardware Components

| Component                   | Quantity |
| --------------------------- | -------- |
| Arduino Uno                 | 1        |
| Ultrasonic Sensor HC-SR04   | 1        |
| LM35 Temperature Sensor     | 1        |
| 16x2 LCD Display            | 1        |
| Servo Motor (Pump Sim)      | 1        |
| RGB LEDs (Red, Blue, Green) | 1 each   |
| Buzzer                      | 1        |
| Breadboard + Wires          | 1 set    |
| USB Cable + PC              | 1        |

---

### 1. ✅ System in Safe State Tank Full

![Safe State Full](https://github.com/user-attachments/assets/0555bae3-a1a4-4405-a0dc-ca752fc96c3e)

---

### 2. ✅ System in Safe State 

![Safe State](https://github.com/user-attachments/assets/213304d7-f619-4411-83a3-17114325767b)

---

### 3. 🚨 Low Water Level – Pump ON + Buzzer Active

![low_water_alert](https://github.com/user-attachments/assets/75586854-497f-427a-b11c-1f1e7be09859)

---

### 4. 🔧 Full Tinkercad Circuit Layout

![Circuit Layout](https://github.com/user-attachments/assets/bc01bb86-4cee-469c-be4f-52ab81efa2b4)


---

## 👨‍💻 Source Code

```cpp
#include <LiquidCrystal.h>
#include <Servo.h>

LiquidCrystal lcd(12, 11, 5, 4, 3, 2);
Servo pump;

// Pins
int trig = 10;
int echo = 9;
int buzzer = 6;
int redLED = A1;
int greenLED = A2;
int blueLED = A3;
int tempPin = A0;  // LM35 connected here

void setup() {
  Serial.begin(9600);
  lcd.begin(16, 2);
  pump.attach(7);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);
  pinMode(buzzer, OUTPUT);
  pinMode(redLED, OUTPUT);
  pinMode(greenLED, OUTPUT);
  pinMode(blueLED, OUTPUT);

  lcd.setCursor(0, 0);
  lcd.print("Smart Water Level");
  delay(2000);
  lcd.clear();
}

void loop() {
  // Trigger Ultrasonic
  digitalWrite(trig, LOW); delayMicroseconds(2);
  digitalWrite(trig, HIGH); delayMicroseconds(10);
  digitalWrite(trig, LOW);

  // Read Distance
  int duration = pulseIn(echo, HIGH);
  float distance = duration * 0.0343 / 2;
  float tankHeight = 50.0;
  float waterLevel = tankHeight - distance;
  float volumeLiters = (waterLevel * 625.0) / 1000.0;

  // Read temperature from LM35
  int tempRaw = analogRead(tempPin);
  float temperature = tempRaw * (5.0 / 1023.0) * 100; // Convert to Celsius

  // Serial Output
  Serial.print("Distance: "); Serial.print(distance); Serial.println(" cm");
  Serial.print("Water Level: "); Serial.print(waterLevel); Serial.println(" cm");
  Serial.print("Volume: "); Serial.print(volumeLiters); Serial.println(" Liters");
  Serial.print("Temperature: "); Serial.print(temperature); Serial.println(" C");
  Serial.println("--------------------------");

  // LCD Display
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Lvl:");
  lcd.print((int)waterLevel);
  lcd.print(" ");
  lcd.print((int)volumeLiters);
  lcd.print("L");

  lcd.setCursor(0, 1);
  lcd.print((int)temperature);
  lcd.print("C ");

  if (distance > 35) {
    lcd.print("Low!");
    pump.write(90); // Simulate pump ON
    digitalWrite(buzzer, HIGH);
    setLED("RED");
  } else if (distance <= 35 && distance > 15) {
    lcd.print("OK ");
    pump.write(0);
    digitalWrite(buzzer, LOW);
    setLED("BLUE");
  } else {
    lcd.print("Full");
    pump.write(0);
    digitalWrite(buzzer, LOW);
    setLED("GREEN");
  }

  delay(2000);
}

void setLED(String level) {
  digitalWrite(redLED, level == "RED");
  digitalWrite(greenLED, level == "GREEN");
  digitalWrite(blueLED, level == "BLUE");
}
```

---

## 🔍 Working Principle

* **Ultrasonic Sensor (HC-SR04)**: Measures the water surface distance.
* **Calculation**: `WaterLevel = TankHeight - Distance`
* **Volume (Liters)**: Approximated using tank area.
* **Temperature**: Read from LM35, converted to Celsius.
* **Servo**: Acts as an automated water pump.
* **LEDs + Buzzer**: Alerts based on:

  * 🔴 Red + Buzzer → Low Level
  * 🔵 Blue → Mid Level
  * 🟢 Green → Full Tank

---

## 🔗 Tinkercad Simulation Link

👉 [Click here to open the simulation](https://www.tinkercad.com/things/d56eTb9bCVC-intelligent-water-tank-monitoring-and-auto-pump-control-system?sharecode=XLOpoPNToWHLZL4MjqH5tiRE5A-VF-2woUzPixh3Mas)

---

## 🙏 Acknowledgements

* Developed by **Shaili Sahu**
* Built using Tinkercad + Arduino Uno
* Inspired by common water conservation and automation needs
* Special thanks to open-source contributors for helping with logic and examples

