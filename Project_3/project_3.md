# Project 3

## Part 1

### Suðukóði

```
def LightsOff() { 
	drivetrain.stop()
	wait_until(lightsensor.value() < 2)
}

def CheckAround() {
	turn(left, 90deg)
	if distance > 1meter { return }
	turn(right, 180deg)
	if distance > 1meter { return }
	turn(right, 90deg)
}

void main {

	light.changed(LightsOff)

	white(true) {
		drivetrain.drive(forward)

		if distance < 0.5meter {
			drivetrain.stop()
			CheckAround()
		}
	}
```

### Main:

[Testrun video](https://youtu.be/P1BteJjgTnA)

```cpp
#include "vex.h"

using namespace vex;

bool lightOn = true;

void lightChanged() { //run when the light value changes and updates lightOn variable and stops drivetrain when lights are off
  if (Light.brightness() < 55) {
    lightOn = false;
    Drivetrain.stop();
    waitUntil(Light.brightness() > 55);
    lightOn = true;
  }
}

int Screen() { //thread for printing information
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

int CheckAround() { //when the car is about to crash looks around for a new direction
  Drivetrain.turnFor(-90, deg);
  wait(0.5, sec);
  if (RangeFinderC.distance(mm) > 1000) {return 0;}
  Drivetrain.turnFor(180, deg);
  wait(0.5, sec);
  if (RangeFinderC.distance(mm) > 1000) {return 0;}
  Drivetrain.turnFor(90, deg);
  wait(0.5, sec);
  return 0;
}

int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();

  // Wait 1 second before driving forward.
  wait(1, seconds);

  Light.changed(lightChanged);
  thread ScreenPrint = thread(Screen);

  // The robot will stop driving when the Range Finder is less than 500 mm away for an object
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

## Part 2

![Screenshot 2025-02-13 090930](https://github.com/user-attachments/assets/b7fe88f6-a260-4aca-a8f3-088d2577b82c)

We connected to the VEX V5 Vision Sensor using our phones by accessing the VEX V5 Brain’s built-in Wi-Fi hotspot. After enabling the Vision Sensor on the VEX Brain, We connected our phones to the Wi-Fi network it created. Then, We opened a web browser and entered the Brain’s IP address to access the live camera feed. This allowed us to view real-time vision data, adjust settings, and fine-tune object detection directly from our phones.
## Part 3

### Main:
> Object sensor detecting colors and objects
```cpp
#include "vex.h"

using namespace vex;

event checkRed = event();

const int CENTER_FOV = 150;   // Random value between 148 and 168
const int OFFSET_X = 20;      // Allowable deviation from the center
const int STOP_DISTANCE = 80; // Stop when object is within 40 cm

int array[10] = {};
int estimatedDistance = 0;

int getDistancethread() { //Create thread
    while (true) {
        Vision5.takeSnapshot(Vision5__GREENBOX);

        if (Vision5.objectCount > 0) {  // If green object is detected
            int objectWidth = Vision5.largestObject.width;

            Brain.Screen.clearScreen();
            Brain.Screen.setCursor(1, 1);
            Brain.Screen.print("Green Object Found: %d", objectWidth);
            
            for (int i = 0; i < 9; i++) {
                array[i] = array[i + 1];  // Shift elements to the left excluding the first
            }
            array[9] = objectWidth;

            int sum = 0;
            for(int i=0; i<10; i++) {
                sum += array[i];
            } 

            estimatedDistance = sum/10; // Averages the last 10 readings from the sensor for a less varied result 

            Brain.Screen.setCursor(2, 1);
            Brain.Screen.print("Distance: %d cm", estimatedDistance);

            wait(20, msec);
        }
    }
    return 0;
}

int main() {
    // Initializing Robot Configuration. DO NOT REMOVE!
    vexcodeInit();

    //Drivetrain.setDriveVelocity(20, percent); // Moderate speed for tracking

    thread runThread = thread(getDistancethread); // Start estimating distance

    Drivetrain.setTurnVelocity(5, percent);

    int canDrive = 0; 

    while (true) {
        Vision5.takeSnapshot(Vision5__GREENBOX);

        if (Vision5.objectCount > 0) {
            int objectX = Vision5.largestObject.centerX;
            int error = objectX - CENTER_FOV;

            if (estimatedDistance >= STOP_DISTANCE) { // Based on width stops driving when the box comes too close
                canDrive = 0;
                if(estimatedDistance >= STOP_DISTANCE*1.2) {Drivetrain.stop();} //offsets the initial stop to make up for inconsistency in the sensor readings
                Brain.Screen.setCursor(3, 1);
                Brain.Screen.print("Object within 40cm - Stopping");
            } else {
                if(estimatedDistance<STOP_DISTANCE) { canDrive += 1;} // counts how many times the distance is far enough to begin driving again
                LeftMotor.setVelocity(20+error/15, percent); //set the velocity of both wheels based on where the box is to make it turn in the right direction
                RightMotor.setVelocity(20-error/15, percent);
                
                if(canDrive>5) { // drives again only once being given the green light 5 times to rule out the inconsistant readings further
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


## Config:
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
