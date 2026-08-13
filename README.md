# 🐕 QuadWalker — Arduino 4-Legged Walking Robot

[![Platform](https://img.shields.io/badge/Platform-Arduino%20Uno-blue?style=flat-square)]()
[![Language](https://img.shields.io/badge/Language-C++-orange?style=flat-square)]()
[![Status](https://img.shields.io/badge/Status-Active%20%26%20Verified-success?style=flat-square)]()

> *"Engineering is a matter of making things work."* ⚡✨

3D-printed body + 4 servos + Arduino Uno = a small robot that walks forward and backward on four legs.

<!-- ضعي هنا رابط صورة الروبوت النهائي -->
<img width="1280" height="960" alt="IMG_7973" src="https://github.com/user-attachments/assets/f7b3cff7-d233-4e99-a616-3fc13d1c594f" />


---

## 📖 About

QuadWalker is an engineered 4-legged robotic system designed to simulate quadruped locomotion using compact embedded hardware. Each leg is driven by a single servo motor with a curved, paddle-shaped limb design. The rotation of the servo directly translates into ground contact and directional force, eliminating the need for complex multi-axis lifting mechanisms.

The system relies on an **Arduino Uno** as the central processing unit, communicating through a breadboard power/signal distribution layer to actuate the precise joint angles required for balanced movement.

---

## 🧠 Hardware Architecture

<!-- ضعي هنا رابط صورة القطع والمكونات -->
<img width="1280" height="724" alt="IMG_7974" src="https://github.com/user-attachments/assets/f2cafce9-eb19-4578-b26c-8e923b6f2d45" />


| Component | Qty | Engineering Notes |
| :--- | :--- | :--- |
| Arduino Uno | 1 | Central microcontroller powered via USB or external supply |
| Servo motor (SG90) | 4 | High-torque micro servos acting as joint actuators (one per leg) |
| Breadboard | 1 | Circuit distribution layer for power, ground, and PWM signals |
| 3D printed body | 1 | Custom structural chassis designed with dedicated servo mounting slots |
| Jumper Wires | as needed | Male-Male and Male-Female interconnections |

---

## 🛠 Mechanical Assembly

The assembly process integrates the 3D-printed structural chassis with the electronic actuators. The four micro servos are securely mounted inside the body framework (two at each end), allowing the external arm linkages to transfer rotational force directly into leg displacement.

<!-- ضعي هنا رابط صورة التجميع -->
<img width="1280" height="695" alt="IMG_7978" src="https://github.com/user-attachments/assets/72c38c51-88a2-4e4c-b0bb-e5af154c9d28" />


---

## 🔌 Circuit Wiring & Pinout

Each servo motor utilizes a standard 3-wire interface: Signal (PWM), Power ($V_{CC}$), and Ground ($GND$).

| Leg Identifier | Arduino Digital Pin | Actuator Object |
| :--- | :--- | :--- |
| Front Left (FL) | Pin 3 | `legFL` |
| Front Right (FR) | Pin 5 | `legFR` |
| Back Left (BL) | Pin 6 | `legBL` |
| Back Right (BR) | Pin 9 | `legBR` |

> ⚠️ **Power Notice:** Connect all servo power and ground lines to the distribution rails on the breadboard, linked directly to the 5V and GND pins of the Arduino. For stable multi-servo operation without voltage drops, an external 5V power supply is strongly recommended.

<!-- ضعي هنا رابط صورة التوصيلات -->
<img width="1095" height="589" alt="IMG_7973" src="https://github.com/user-attachments/assets/3c06dfe2-382e-4e02-b7ed-6a0d733ea3e9" />


---

## 🔄 Kinematic Control Logic

The firmware manages movement through synchronized dual-limb pairing to ensure continuous stability:
1. **Center Stand (`standCenter`):** Initializes all joints to a neutral 90° reference angle.
2. **Forward Locomotion (`stepForward`):** Synchronizes diagonal limb pairs `(FL + BR)` and `(FR + BL)` to alternate movement offsets and propel the chassis forward.
3. **Backward Locomotion (`stepBackward`):** Reverses the phase sequence to achieve smooth rearward displacement.

---

## 💻 Arduino Sketch

```cpp
#include <Servo.h>

Servo legFL; // Front Left - Pin 3 
Servo legFR; // Front Right - Pin 5 
Servo legBL; // Back Left - Pin 6 
Servo legBR; // Back Right - Pin 9

const int PIN_FL = 3;
const int PIN_FR = 5;
const int PIN_BL = 6;
const int PIN_BR = 9;

int centerAngle = 90;   
int swingAngle  = 30;   
int stepDelay   = 200;  

void setup() {
  legFL.attach(PIN_FL);
  legFR.attach(PIN_FR);
  legBL.attach(PIN_BL);
  legBR.attach(PIN_BR);

  legFL.write(centerAngle);
  legFR.write(centerAngle);
  legBL.write(centerAngle);
  legBR.write(centerAngle);
  delay(1000);
}

void stepForward() {
  int a1 = centerAngle - swingAngle;
  int a2 = centerAngle + swingAngle;

  legFL.write(a1);
  legBR.write(a1);
  legFR.write(a2);
  legBL.write(a2);
  delay(stepDelay);

  legFL.write(a2);
  legBR.write(a2);
  legFR.write(a1);
  legBL.write(a1);
  delay(stepDelay);
}

void stepBackward() {
  int a1 = centerAngle - swingAngle;
  int a2 = centerAngle + swingAngle;

  legFL.write(a2);
  legBR.write(a2);
  legFR.write(a1);
  legBL.write(a1);
  delay(stepDelay);

  legFL.write(a1);
  legBR.write(a1);
  legFR.write(a2);
  legBL.write(a2);
  delay(stepDelay);
}

void standCenter() {
  legFL.write(centerAngle);
  legFR.write(centerAngle);
  legBL.write(centerAngle);
  legBR.write(centerAngle);
}

.loop() {
  for (int i = 0; i < 5; i++) stepForward();
  standCenter();
  delay(1000);

  for (int i = 0; i < 5; i++) stepBackward();
  standCenter();
  delay(1000);
}

```

## ⚙️ How to Run

1. Open the Arduino IDE.
2. Verify that the native `Servo` library is included.
3. Open or paste the provided firmware sketch.
4. Select the target board Arduino Uno and assign the correct serial Port.
5. Compile and Upload the code to the microcontroller.
6. Observe the execution cycle: 5 steps forward, stabilization pause, 5 steps backward, and loop repetition. 🐾

---

## 📌 Engineering Notes

* Verify physical pin connections match the configuration constants (`PIN_FL`, `PIN_FR`, etc.).
* If an individual leg moves in the reverse direction, swap variables `a1` and `a2` for that specific actuation function.
* Adjust `swingAngle` between 15° and 20° for finer, more controlled calibration steps.

## 📁 Repository Structure

```text
arduino-quadruped-robot/
├── code/
│   └── quadruped_control.ino    # Main C++ Arduino firmware script
├── docs/                        # Hardware schematics and assembly photos
└── README.md                    # Technical documentation & system manual

```

