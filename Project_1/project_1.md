# Project 1

## Codes:
### Main:
```py
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

### Robot-Config:
```py
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain Brain;

// VEXcode device constructors
motor LeftDriveSmart = motor(PORT1, ratio18_1, false);
motor RightDriveSmart = motor(PORT3, ratio18_1, true);
gyro TurnGyroSmart = gyro(Brain.ThreeWirePort.D);
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
  // reset the screen now that the calibration is complete
  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1, 1);
  wait(50, msec);
  Brain.Screen.clearScreen();
}
```
