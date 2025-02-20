# 🚀 Project 3

## 📌 Part 1: Welding Code

### 🔹 Welding Code Implementation
```
def LightsOff() { 
	drivetrain.stop();
	wait_until(lightsensor.value() < 2);
}

def CheckAround() {
	turn(left, 90deg);
	if (distance > 1meter) { return; }
	turn(right, 180deg);
	if (distance > 1meter) { return; }
	turn(right, 90deg);
}

void main {
	light.changed(LightsOff);
	white(true) {
		drivetrain.drive(forward);
		if (distance < 0.5meter) {
			drivetrain.stop();
			CheckAround();
		}
	}
}
```

### 🎥 Testrun Video
[![Testrun](https://img.shields.io/badge/Click-Here!-red?style=for-the-badge)](https://youtu.be/P1BteJjgTnA)

### 📝 Description
> The VEX Robot detects obstacles and determines the best pathway to drive forward.

### 🔹 Main Code Implementation
```cpp
#include "vex.h"

using namespace vex;

bool lightOn = true;

void lightChanged() { 
	if (Light.brightness() < 55) {
		lightOn = false;
		Drivetrain.stop();
		waitUntil(Light.brightness() > 55);
		lightOn = true;
	}
}

int Screen() {
	while(true) {
		Brain.Screen.setCursor(1, 1);
		Brain.Screen.print("%.2f", RangeFinderC.distance(mm));
		Brain.Screen.setCursor(2, 1);
		Brain.Screen.print(Light.brightness());
		Brain.Screen.setCursor(3, 1);
		Brain.Screen.print(lightOn);
		wait(100, msec);
		Brain.Screen.clearScreen();
	}
	return 0;
}

int CheckAround() {
	Drivetrain.turnFor(-90, deg);
	wait(0.5, sec);
	if (RangeFinderC.distance(mm) > 1000) { return 0; }
	Drivetrain.turnFor(180, deg);
	wait(0.5, sec);
	if (RangeFinderC.distance(mm) > 1000) { return 0; }
	Drivetrain.turnFor(90, deg);
	wait(0.5, sec);
	return 0;
}

int main() {
	vexcodeInit();
	wait(1, seconds);
	Light.changed(lightChanged);
	thread ScreenPrint = thread(Screen);

	while (true) {
		if (lightOn) { Drivetrain.drive(forward); } else Drivetrain.stop();
		if (RangeFinderC.distance(mm) < 500 && lightOn) {
			Drivetrain.stop();
			wait(0.5, sec);
			CheckAround();
		}   
	}
}
```

---

## 📌 Part 2: Vision Sensor Connection

### 🎥 Vision Sensor Setup
📸 **Connecting VEX V5 Vision Sensor using a Phone**

![Vision Sensor](https://github.com/user-attachments/assets/b7fe88f6-a260-4aca-a8f3-088d2577b82c)

- **Step 1**: Enabled Vision Sensor on the VEX Brain.
- **Step 2**: Connected to the Wi-Fi network created by the Brain.
- **Step 3**: Entered Brain’s IP address in a web browser.
- **Step 4**: Accessed the live camera feed for real-time object detection.

---

## 📌 Part 3: Object Detection with Sensors

### 🎥 Testrun Video
[![Testrun](https://img.shields.io/badge/Watch-Video-red?style=for-the-badge)](https://youtu.be/Y1HiajOZmJc)

### 🔹 Object Sensor Code
```cpp
#include "vex.h"

using namespace vex;

event checkRed = event();

const int CENTER_FOV = 150; 
const int OFFSET_X = 20;
const int STOP_DISTANCE = 60;

int array[10] = {};
int estimatedDistance = 0;

int getDistancethread() {
	while (true) {
		Vision5.takeSnapshot(Vision5__GREENBOX);
		if (Vision5.objectCount > 0) {
			int objectWidth = Vision5.largestObject.width;
			Brain.Screen.clearScreen();
			Brain.Screen.setCursor(1, 1);
			Brain.Screen.print("Green Object Found: %d", objectWidth);
			for (int i = 0; i < 9; i++) { array[i] = array[i + 1]; }
			array[9] = objectWidth;
			int sum = 0;
			for(int i=0; i<10; i++) { sum += array[i]; }
			estimatedDistance = sum/10;
			Brain.Screen.setCursor(2, 1);
			Brain.Screen.print("Distance: %d cm", estimatedDistance);
			wait(20, msec);
		}
	}
	return 0;
}

int main() {
	vexcodeInit();
	thread runThread = thread(getDistancethread);
	int canDrive = 0;
	while (true) {
		Vision5.takeSnapshot(Vision5__GREENBOX);
		if (Vision5.objectCount > 0) {
			int objectX = Vision5.largestObject.centerX;
			int error = objectX - CENTER_FOV;
			if (estimatedDistance >= STOP_DISTANCE) {
				canDrive = 0;
				if(estimatedDistance >= STOP_DISTANCE*1.2) {Drivetrain.stop();}
				Brain.Screen.setCursor(3, 1);
				Brain.Screen.print("Object within 40cm - Stopping");
				Drivetrain.setDriveVelocity(10, percent);
				while(estimatedDistance > STOP_DISTANCE*2) {Drivetrain.drive(reverse);}
			} else {
				if(estimatedDistance < STOP_DISTANCE) { canDrive += 1; }
				LeftMotor.setVelocity(20+error/15, percent);
				RightMotor.setVelocity(20-error/15, percent);
				if(canDrive > 5) {
					LeftMotor.spin(forward);
					RightMotor.spin(forward);
				}
				wait(0.1, sec);
			}
		} else {
			Drivetrain.stop();
		}
	}
}
```

### 🎯 Object Detection in Action
<p align="center">
  <img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExc216ZmY3N2c5Zml0eXJzamlnc2tucHNyYm82Z3drZXVycmRna2tndCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/NcECxf2Rurrq2VL0a9/giphy.gif" width="650" />
</p>

---

## ⚙️ Configuration Code
```cpp
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain Brain;

// VEXcode device constructors
/*vex-vision-config:begin*/
motor LeftMotor = motor(PORT1, ratio18_1, false);
motor RightMotor = motor(PORT3, ratio18_1, true);
drivetrain Drivetrain = drivetrain(LeftMotor, RightMotor, 319.19, 295, 40, mm, 1);
sonar RangeFinderC = sonar(Brain.ThreeWirePort.D);
light Light = light(Brain.ThreeWirePort.E);
signature Vision5__BLUEBOX =
    signature(1, -3441, -2785, -3113, 8975, 10355, 9665, 2.5, 0);
signature Vision5__GREENBOX =
    signature(2, -5767, -4965, -5366, -3803, -2861, -3332, 2.5, 0);
signature Vision5__REDBOX =
    signature(3, 8099, 8893, 8496, -1505, -949, -1227, 2.5, 0);
signature Vision5__SIG_4 = signature(4, 0, 0, 0, 0, 0, 0, 2.5, 0);
signature Vision5__SIG_5 = signature(5, 0, 0, 0, 0, 0, 0, 2.5, 0);
signature Vision5__SIG_6 = signature(6, 0, 0, 0, 0, 0, 0, 2.5, 0);
signature Vision5__SIG_7 = signature(7, 0, 0, 0, 0, 0, 0, 2.5, 0);
vision Vision5 =
    vision(PORT8, 50, Vision5__BLUEBOX, Vision5__GREENBOX, Vision5__REDBOX,
           Vision5__SIG_4, Vision5__SIG_5, Vision5__SIG_6, Vision5__SIG_7);
/*vex-vision-config:end*/

// VEXcode generated functions

/**
 * Used to initialize code/tasks/devices added using tools in VEXcode Pro.
 *
 * This should be called at the start of your int main function.
 */
void vexcodeInit(void) {
  // nothing to initialize
}
```

---

### 📜 License
📌 This project is open-source and follows standard licensing practices.
