# Project 2

### Íslenska:
Hver rofi er hæfur að stjórna bílnum í allar áttir. Með vinstri rofanum keyrir bílinn hratt, með hægri rofanum keyrir bílinn hægt fyrir meiri nákvæmni. Annars er stjórnun með báðum rofum alveg eins.
Til þess að stjórna krananum eru notaður L1 til að fara upp og L2 til að fara niður og svo nota R1 til að opna klónna og R2 til að loka henni.

### English:
Each joystick is able to control the car in all directions. With the left joystick you can drive fast, and with the right joytick you can be slow and precise. The contols for each joystick is otherwise the same.
To control the crane, use L1 to go up and L2 to go down, then use R1 to open the claw and R2 to close it.

## Part 1:
### Main code:
> Wireless Controller functionality and activity

[Testrun Video](https://youtu.be/ACWBoWRuvFU)

```cpp
#include "vex.h"

using namespace vex;

// Declare Controller event callbacks.
void whenControllerL1Pressed() {
  ArmMotor.spin(forward);
  waitUntil(!Controller1.ButtonL1.pressing());
  ArmMotor.stop();
}
void whenControllerL2Pressed() {
  ArmMotor.spin(reverse);
  waitUntil(!Controller1.ButtonL2.pressing());
  ArmMotor.stop();
}
void whenControllerR1Pressed() {
  ClawMotor.spin(reverse);
  waitUntil(!Controller1.ButtonR1.pressing());
  ClawMotor.stop();
}
void whenControllerR2Pressed() {
  ClawMotor.spin(forward);
  waitUntil(!Controller1.ButtonR2.pressing());
  ClawMotor.stop();
}


void BumperPressed() {
  // Clear the screen line before printing
  Brain.Screen.setCursor(5, 1);
  Brain.Screen.clearLine();

  // Check if the bumper switch is pressed
  if (BumperA.pressing()) {
    // Display message
    Brain.Screen.print("Emergency Stop Activated!");

    // Stop all motors immediately
    LeftMotor.stop();
    RightMotor.stop();
    ClawMotor.stop();
    ArmMotor.stop();
  }
}



// Emergency stop function when the X button is pressed
void emergencyStop() {
  LeftMotor.stop();
  RightMotor.stop();
  ClawMotor.stop();
  ArmMotor.stop();
}



int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();

  // Initialize the Controller Events
  Controller1.ButtonL1.pressed(whenControllerL1Pressed);
  Controller1.ButtonL2.pressed(whenControllerL2Pressed);
  Controller1.ButtonR1.pressed(whenControllerR1Pressed);
  Controller1.ButtonR2.pressed(whenControllerR2Pressed);

  // Add X button press event for emergency stop
  Controller1.ButtonX.pressed(emergencyStop);

  float fastspeed = 0.5;  // Emergency stop when X is pressed
  float slowspeed = 0.1;  // Emergency stop when X is pressed

  // Set the brake mode and velocity of the ArmMotor and ClawMotor
  ClawMotor.setStopping(hold);
  ArmMotor.setStopping(hold);

  ClawMotor.setVelocity(60, percent);  // Corrected velocity setting

  // Clear the Brain screen at the beginning
  Brain.Screen.clearScreen();

  // Use tank drive to control the robot.
  while (true) {

    LeftMotor.setVelocity((Controller1.Axis3.position() + Controller1.Axis4.position())*fastspeed, percent);
    RightMotor.setVelocity((Controller1.Axis3.position() - Controller1.Axis4.position())*fastspeed, percent);

    if(Controller1.Axis3.position() == 0 && Controller1.Axis4.position() == 0) {
      LeftMotor.setVelocity((Controller1.Axis2.position() + Controller1.Axis1.position())*slowspeed, percent);
      RightMotor.setVelocity((Controller1.Axis2.position() - Controller1.Axis1.position())*slowspeed, percent);
    }

    
    LeftMotor.spin(forward);
    RightMotor.spin(forward);



    // Display motor velocities and joystick values on the screen
    Brain.Screen.setCursor(1, 1);
    Brain.Screen.print("Left Motor Velocity: %d", LeftMotor.velocity(percent));
    Brain.Screen.setCursor(2, 1);
    Brain.Screen.print("Right Motor Velocity: %d", RightMotor.velocity(percent));

    Brain.Screen.setCursor(3, 1);
    Brain.Screen.clearLine(3);
    Brain.Screen.print("Joystick X: %d", Controller1.Axis3.position());
    Brain.Screen.setCursor(4, 1);
    Brain.Screen.clearLine(4);
    Brain.Screen.print("Joystick Y: %d", Controller1.Axis4.position());

    BumperA.pressed(BumperPressed);

    wait(25, msec);
  }
}
```

## Part 2

### Emergency stop welding code:

```
thread {
  [--------------------------------------]
  [ all the code from verkefni 1 hluti 2 ]
  [--------------------------------------]
}

void main {
  run thread()

  while (true) {
    if (buttonX_pressing or bumper_pressed) {
      stop thread() 
      stop drivetrain()
    }
  }
}
```

### Emergency stop bumper and X button code

[Testrun Video](https://youtu.be/2j-ayJ0NSB8)

```cpp
#include "vex.h"

using namespace vex;

int drivePuzzle() { // This is the old code from verkefni 1 hluti 2 put into a thread
   
  int rightOrLeft[] = {1,-1,-1,1,1,-1,1,1,-1,1,1,-1,-1,1};
  /*1 for turning right and -1 to turn left*/

  for (int i=0; i<14; i++) {
    int direction = rightOrLeft[i];
        Drivetrain.driveFor(forward, 500, mm);
        wait(0.5, seconds);
        Drivetrain.turnFor(right, 90*direction, degrees);
        wait(0.5, seconds);
  }
  Drivetrain.driveFor(forward, 500, mm);
  return 0;
}

int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();

  thread driver = thread(drivePuzzle); // run the original code

  while (true) // check for emergancy stop inputs and stops the car if need be
  {
    if (stopButton.value() || Controller.ButtonX.pressing()) {
      driver.interruptAll();
      Drivetrain.stop();
    }
  } 
}
```


## Part 3:
This program controls a VEX V5 robot to drive in circular paths using a gyro sensor for accurate rotation tracking.

![Radius 1](https://github.com/user-attachments/assets/3f05ee2f-c10d-4a06-a513-f469897f3ad5)

[Testrun video](https://youtu.be/gvW6Mg6NpgM)

### Main code:
> Precise and accurate radius turns
```cpp
#include "vex.h"

using namespace vex;

// Function to drive the robot in a circle
void driveCircle(float speed, float radius) {
    float leftSpeed = speed;
    float rightSpeed = speed * (1 - 1 / radius);;

    // Set the speeds for left and right motors to create a circle
    LeftMotor.spin(forward, leftSpeed, percent);
    RightMotor.spin(forward, rightSpeed, percent);
}

// Function to drive multiple circles using the gyro
int circleDriveWithGyro() {
    float radii[] = {1.5, 2, 2.5};  // Radii for the circles
    float speed = 50;

    // Reset the gyro sensor
    TurnGyroSmart.resetRotation();

    for (int i = 0; i < 3; i++) {
        float radius = radii[i];

        // Start driving in a circle
        driveCircle(speed, radius);

        // Wait until the robot completes a 360° turn
        while (TurnGyroSmart.rotation(degrees) <= 360+i*3) {
            wait(20, msec);  // Prevent excessive CPU usage
        }

        // Stop motors briefly after completing the circle
        LeftMotor.stop();
        RightMotor.stop();

        wait(100, msec);

        // Reset the gyro for the next circle
        TurnGyroSmart.resetRotation();

        wait(100, msec);
    }

    // Stop the robot completely
    LeftMotor.stop();
    RightMotor.stop();
    
    return 0;
}

int main() {
    // Initialize devices and the robot
    vexcodeInit();

    // Execute circle driving logic with thread
    thread driveCircle = thread(circleDriveWithGyro);

    // Check for emergancy stop inputs and stop the robot if needed
    while (true) {
      if (BumperA.value() || Controller1.ButtonX.pressing()) {
        driveCircle.interruptAll();
        LeftMotor.stop();
        RightMotor.stop();
      }
    }
}
```

## Robot-Config:
```cpp
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain Brain;

// VEXcode device constructors
controller Controller1 = controller();
motor LeftMotor = motor(PORT1, ratio18_1, false);
motor RightMotor = motor(PORT3, ratio18_1, true);
motor ClawMotor = motor(PORT4, ratio18_1, false);
motor ArmMotor = motor(PORT5, ratio18_1, false);
bumper BumperA = bumper(Brain.ThreeWirePort.A);
gyro TurnGyroSmart = gyro(Brain.ThreeWirePort.H);
smartdrive Drivetrain = smartdrive(LeftMotor, RightMotor,
                                   TurnGyroSmart, 319.19, 320, 130, mm, 1);


// VEXcode generated functions

/**
 * Used to initialize code/tasks/devices added using tools in VEXcode Pro.
 *
 * This should be called at the start of your int main function.
 */
// VEXcode generated functions
void vexcodeInit(void) {
  Brain.Screen.print("Device initialization...");
  Brain.Screen.setCursor(2, 1);
  
  wait(200, msec);
  TurnGyroSmart.startCalibration(1);
  Brain.Screen.print("Calibrating Gyro for Drivetrain");
  
  // Wait for gyro calibration to finish
  while (TurnGyroSmart.isCalibrating()) {
    wait(25, msec);
  }

  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1, 1);
  wait(50, msec);
  Brain.Screen.clearScreen();
}
```

