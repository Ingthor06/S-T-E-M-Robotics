# Project 2

### Íslenska:
Öll keyrsla á bílnum er strjórnað með einungis vinstri rofanum, bæði til að keyra beint fram og aftan og til að beygja.
Til þess að stjórna krananum eru notaður L1 til að fara upp og L2 til að fara niður og svo nota R1 til að opna klónna og R2 til að loka henni.

### English:
All driving of the car is controlled with the left switch only, both for driving straight forward and backward and for turning. To control the crane, use L1 to go up and L2 to go down, then use R1 to open the claws and R2 to close them.

## Part 1:
### Main:
> Main code for **Part 1** including left stick movement and wireless controls
```py
  while (true) {
    // Set the left and right motor velocities based on the joystick position
    LeftMotor.setVelocity(Controller1.Axis3.position() + Controller1.Axis4.position(), percent);
    RightMotor.setVelocity(Controller1.Axis3.position() - Controller1.Axis4.position(), percent);

    // Spin the motors forward, as their velocities are set by the joysticks
    LeftMotor.spin(forward);
    RightMotor.spin(forward);

    // Delay for more precise controls
    wait(25, msec);
  }
```

## Part 2:
### Main:
```py
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

    if(Controller1.Axis3.position() == 0 && Controller1.Axis3.position() == 0) {
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

    wait(25, msec);
  }
}
```

### Hluti 2 stoppa biliin kóði:

```py
#include "vex.h"

using namespace vex;

int drivePuzzle() {
   
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

  thread driver = thread(drivePuzzle);

  while (true)
  {
    if (stopButton.value() || Controller.ButtonX.pressing()) {
      driver.interruptAll();
      Drivetrain.stop();
    }
  } 
}
```


## Part 3:

![Radius 1](https://github.com/user-attachments/assets/3f05ee2f-c10d-4a06-a513-f469897f3ad5)


## Robot-Config:
```py
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain  Brain;

// VEXcode device constructors
controller Controller1 = controller(primary);
motor LeftMotor = motor(PORT1, ratio18_1, false);
motor RightMotor = motor(PORT3, ratio18_1, true);
motor ClawMotor = motor(PORT4, ratio18_1, false);
motor ArmMotor = motor(PORT5, ratio18_1, false);

// VEXcode generated functions

/**
 * Used to initialize code/tasks/devices added using tools in VEXcode Pro.
 * 
 * This should be called at the start of your int main function.
 */
void vexcodeInit( void ) {
  // nothing to initialize
}
```

