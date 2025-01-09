# Project 1
In Project 1, we focus on programming the robot to perform a sequence of movements, starting with simple tasks and gradually increasing complexity. The robot will move forward and backward in set increments, using loops and logic to automate the process. We will document the process with flowcharts, write clean and efficient code, and demonstrate the robot's functionality through videos.

## Part 1:
> The welding code for part 1

```
for (i = 1..5) {
  move(forward, 500*i, mm)
  move(backward, 500*i, mm)
}
```
### Main:
> Main code for **Part 1** including movement functions and delays
```cpp
#include "vex.h"

using namespace vex;

int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();

  for (int i=1; i <=5; i++) {
    Drivetrain.driveFor(forward, i*500, mm);
    
    // Stop for 1 second
    wait(1, seconds);
    
    // Drive backward for 150 mm
    Drivetrain.driveFor(reverse, i*500, mm);

    wait(1, seconds);
  }  
}
```
### Video:
View the movement fuctions testing in this short [video](https://www.youtube.com/watch?v=wdl-D-8D_Zs).

## Part 2:

> The welding code for part 2

```
list = [1,-1,-1,1,1,-1,1,1,-1,1,1,-1,-1,1]
# 1 turn to the right and -1 turn to the right

for i in list {
  move(forward, 50, mm)
  rotate(90*i)
}
move(forward, 50, mm)
```

### Main:
> Main code for **Part 2** including movement functions and delays
```py
#include "vex.h"

using namespace vex;

int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();
  // turns the robot left 90 degrees
  int rightOrLeft[] = {1,-1,-1,1,1,-1,1,1,-1,1,1,-1,-1,1};

  for(int i = 0; i < 14; i++){
    int direction = rightOrLeft[i];
    Drivetrain.driveFor(forward, 500, mm);
    wait(0.5, seconds);
    Drivetrain.turnFor(right, 90*direction, degrees);
    wait(0.5, seconds);
  }
  Drivetrain.driveFor(forward, 500, mm);
}
```
### Video:
Check out the movement fuctions testing in this short [video](https://youtu.be/0hxD540YdD4).

## Robot-Config:
> Robot configuration maps and initializes hardware components for proper control.
```cpp
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain Brain;

// VEXcode device constructors
motor LeftDriveSmart = motor(PORT1, ratio18_1, false);
motor RightDriveSmart = motor(PORT3, ratio18_1, true);
gyro TurnGyroSmart = gyro(Brain.ThreeWirePort.H);
smartdrive Drivetrain = smartdrive(LeftDriveSmart, RightDriveSmart,
                                   TurnGyroSmart, 319.19, 320, 130, mm, 1);

// VEXcode generated functions

/**
 * Used to initialize code/tasks/devices added using tools in VEXcode Pro.
 *
 * This should be called at the start of your int main function.
 */
void vexcodeInit(void) {
  Brain.Screen.print("Device initialization...");
  Brain.Screen.setCursor(2, 1);
  // calibrate the drivetrain gyro
  wait(200, msec);
  TurnGyroSmart.startCalibration(1);
  Brain.Screen.print("Calibrating Gyro for Drivetrain");
  // wait for the gyro calibration process to finish
  while (TurnGyroSmart.isCalibrating()) {
    wait(25, msec);
  }

  Drivetrain.setTurnVelocity(100, rpm);
  Drivetrain.setDriveVelocity(100, rpm);
  // reset the screen now that the calibration is complete
  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1, 1);
  wait(50, msec);
  Brain.Screen.clearScreen();
}
```
