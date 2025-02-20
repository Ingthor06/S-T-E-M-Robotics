# **Project 4: Line Tracking Robot**  

### **Overview**  
This project is a VEX V5 robot program designed to follow a line using three line sensors. The robot adjusts its speed and direction based on sensor readings.  

---

## **📌 Features**  
✔️ **Line tracking** using three sensors (Left, Center, Right).  
✔️ **Adjustable motor velocity** based on sensor inputs.  
✔️ **Screen output on VEX Brain** to display robot status.  
✔️ **Memory function** to recall last detected line position.  

---

## **📜 Part 1: Line Tracking Code**  

### 🔧 **Welding Code (Line Following Logic)**  
```
bothmotors.setvelocity(50%)

int threshold = 30;

while(true) {
    if (linefound) {
        bothwheels.spin();
    } else {
        bothwheels.stop();
    }

    int leftmotorvelocity = 50;
    int rightmotorvelocity = 50;

    if (leftsensor.value() >= threshold && rightsensor.value() < threshold) {
        leftmotorvelocity -= 20;
        if (centersensor.value() > threshold) {
            leftmotorvelocity += 10;
        }
    }
    if (rightsensor.value() >= threshold && leftsensor.value() < threshold) {
        rightmotorvelocity -= 20;
        if (centersensor.value() > threshold) {
            rightmotorvelocity += 10;
        }
    }
    leftmotor.setvelocity(leftmotorvelocity, percent);
    rightmotor.setvelocity(rightmotorvelocity, percent);
}
```

---

### 🎥 **Demonstration Video**  
📌 Watch the educational video: [Click Here](https://youtu.be/dP-LQtYy-pw)  

---

## **🖥️ Main Program (Brain Display + Line Tracking Logic)**  
```cpp
#include "vex.h"

using namespace vex;

int threshold;
int status = 0;
int last;

// Function to print the status of the robot on the Brain screen
int PrintOnScreen() {
  while(true) {
    Brain.Screen.clearLine(1);
    Brain.Screen.setCursor(1,1);
    switch (status) {
      case 0:
        Brain.Screen.print("Leita af linu..."); // Searching for line
        break;
      case 1:
        Brain.Screen.print("Lina til vinstri..."); // Line detected on left
        break;
      case 2:
        Brain.Screen.print("Lina i midjunni..."); // Line detected in the middle
        break;
      case 3:
        Brain.Screen.print("Lina til haegri..."); // Line detected on right
        break;
    }
    wait(5, msec);
  }
  return 0;
}

int main() {
  vexcodeInit();
  thread screen = thread(PrintOnScreen);
  
  threshold = 30;
  int leftmotorvelocity;
  int rightmotorvelocity;

  while(true) {
    int L = 100 - LineTrackerL.reflectivity();
    int C = 100 - LineTrackerC.reflectivity();
    int R = 100 - LineTrackerR.reflectivity();
    
    leftmotorvelocity = 15;
    rightmotorvelocity = 15;
    status = 0;

    if (L <= 100 - threshold || C <= 100 - threshold || R <= 100 - threshold) { 
      status = 2;
      LeftMotor.spin(forward);
      RightMotor.spin(forward);
    } else { 
      RightMotor.stop();
      LeftMotor.stop();
      last = 0;
    } 

    if (last == 1) { 
      leftmotorvelocity = 5;
      status = 1;
    } 
    if (last == 2) { 
      rightmotorvelocity = 5;
      status = 3; 
    }

    if (L >= R + threshold) {
        last = 1;
        leftmotorvelocity = 8;
    }
    if (R >= L + threshold) {
        last = 2;
        rightmotorvelocity = 8;
    }

    LeftMotor.setVelocity(leftmotorvelocity, percent);
    RightMotor.setVelocity(rightmotorvelocity, percent);
    wait(10, msec);
  }
}
```

---

### 📸 **Robot in Action**  
<p align="center">
    <img src="https://github.com/user-attachments/assets/1161e463-60ba-4be4-8c65-06a43e7e6b09" alt="Robot following a line" width="650">
</p>

---

## **🛠️ Configuration Code**  
```cpp
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// Brain instance for displaying information
brain Brain;

// Line trackers
line LineTrackerL = line(Brain.ThreeWirePort.E);
line LineTrackerC = line(Brain.ThreeWirePort.F);
line LineTrackerR = line(Brain.ThreeWirePort.G);

// Motors
motor LeftMotor = motor(PORT1, ratio18_1, false);
motor RightMotor = motor(PORT3, ratio18_1, true);

// Function to initialize devices
void vexcodeInit(void) {
  // Nothing to initialize
}
```

---

## **📢 Conclusion**  
> [!NOTE]
> This VEX V5 program enables a robot to autonomously follow a line using three line trackers and dynamically adjust its movement. The program also displays real-time tracking information on the V5 Brain screen.  

---

### **📌 Key Technologies Used**
✔️ C++  
✔️ VEX V5 Robot  
✔️ Multi-threading  
✔️ Sensor Integration  
