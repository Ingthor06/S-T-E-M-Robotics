# Project 2

### Íslenska:
Hver rofi er hæfur að stjórna bílnum í allar áttir. Með vinstri rofanum keyrir bílinn hratt, með hægri rofanum keyrir bílinn hægt fyrir meiri nákvæmni. Annars er stjórnun með báðum rofum alveg eins.
Til þess að stjórna krananum eru notaður L1 til að fara upp og L2 til að fara niður og svo nota R1 til að opna klónna og R2 til að loka henni.

### English:
Each joystick is able to control the car in all directions. With the left joystick you can drive fast, and with the right joytick you can be slow and precise. The contols for each joystick is otherwise the same.
To control the crane, use L1 to go up and L2 to go down, then use R1 to open the claw and R2 to close it.

## Part 1:
### Main code:
```cpp
  float fastspeed = 0.5;  // Right joystick
  float slowspeed = 0.1;  // Right Joystick

  // Set the brake mode and velocity of the ArmMotor and ClawMotor
  ClawMotor.setStopping(hold);
  ArmMotor.setStopping(hold);

  ClawMotor.setVelocity(60, percent);  // Corrected velocity setting

  // Clear the Brain screen at the beginning
  Brain.Screen.clearScreen();

  // Use tank drive to control the robot.
  while (true) {

    // Some mathematics to convert joystick position into corresponding wheel velocity
    LeftMotor.setVelocity((Controller1.Axis3.position() + Controller1.Axis4.position())*fastspeed, percent);
    RightMotor.setVelocity((Controller1.Axis3.position() - Controller1.Axis4.position())*fastspeed, percent);

    if(Controller1.Axis3.position() == 0 && Controller1.Axis3.position() == 0) {
      LeftMotor.setVelocity((Controller1.Axis2.position() + Controller1.Axis1.position())*slowspeed, percent);
      RightMotor.setVelocity((Controller1.Axis2.position() - Controller1.Axis1.position())*slowspeed, percent);
    }

    // Starts moving the wheels according to the given velocities
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

    wait(25, msec);
  }
}
```

## Part 2
### Emergency stop bumper and X button code

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

```py
#include "vex.h"

using namespace vex;

// Function to drive the robot in a circle
void driveCircle(float speed, float radius) {
    float leftSpeed = speed;
    float rightSpeed = speed;

    if (radius > 0) {
        // Larger circle: inside wheel (right) goes slower
        rightSpeed = speed * (1 - 1 / radius);
    } else if (radius < 0) {
        // Negative radius (opposite direction)
        leftSpeed = speed * (1 - 1 / radius);
    }

    // Set the speeds for left and right motors to create a circle
    LeftMotor.spin(vex::directionType::fwd, leftSpeed, vex::velocityUnits::pct);
    RightMotor.spin(vex::directionType::fwd, rightSpeed, vex::velocityUnits::pct);
}

// Function to drive multiple circles using the gyro
void circleDriveWithGyro() {
    float radii[] = {1.5, 2, 2.5};  // Radii for the circles
    int numCircles = sizeof(radii) / sizeof(radii[0]);
    float speed = 50;

    // Reset the gyro sensor
    TurnGyroSmart.resetRotation();

    for (int i = 0; i < numCircles; i++) {
        float radius = radii[i];

        // Start driving in a circle
        driveCircle(speed, radius);

        // Wait until the robot completes a 360° turn
        while (TurnGyroSmart.rotation(degrees) < 360) {
            vex::wait(20, msec);  // Prevent excessive CPU usage
        }

        // Stop motors briefly after completing the circle
        LeftMotor.stop();
        RightMotor.stop();

        // Reset the gyro for the next circle
        TurnGyroSmart.resetRotation();

        // Optional delay between circles
        vex::wait(100, msec);
    }

    // Stop the robot completely
    LeftMotor.stop();
    RightMotor.stop();
}

int main() {
    // Initialize devices and the robot
    vexcodeInit();

    // Execute circle driving logic
    circleDriveWithGyro();

    // Keep the program alive
    while (true) {
        vex::wait(100, msec);
    }
}

```

## Robot-Config:
```py
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain Brain;

// VEXcode device constructors
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

