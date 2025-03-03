# Project 5

> unfinished project 

```cpp
/*----------------------------------------------------------------------------------*/
/*                                                                                  */
/*    Module:             main.cpp                                                  */
/*    Author:             VEX                                                       */
/*    Created:            Wed Jun 09 2021                                           */
/*    Description:        Drive to Location (Known Starting Position)               */
/*                        This example will show how to use a GPS Sensor to         */
/*                        navigate a V5 Moby Hero Bot to the center of a field      */
/*                        by driving along the X-axis then the Y-axis               */
/*                                                                                  */
/*    Starting Position:  Bottom Right Corner - Facing West                         */
/*                                                                                  */
/*----------------------------------------------------------------------------------*/

// ---- START VEXCODE CONFIGURED DEVICES ----
// Robot Configuration:
// [Name]               [Type]        [Port(s)]
// Drivetrain           drivetrain    1, 10, 3        
// ForkMotorGroup       motor_group   2, 9            
// Rotation4            rotation      4               
// GPS8                 gps           8               
// DistanceLeft         distance      12              
// DistanceRight        distance      20              
// Optical19            optical       19              
// BumperA              bumper        A               
// ---- END VEXCODE CONFIGURED DEVICES ----

#include "vex.h"

using namespace vex;

float myAbs(float x) { return (x < 0) ? -x : x; }

void myTurn(double degrees) {
  Drivetrain.setTurnVelocity(25, percent);
  ((degrees-GPS8.heading())<0) ? Drivetrain.turn(left) : Drivetrain.turn(right);
  waitUntil(myAbs(GPS8.heading()-degrees)<5);
  Drivetrain.stop();
  wait(0.5,seconds);
  while(myAbs(GPS8.heading()-degrees)>1) {
    Drivetrain.setTurnVelocity(5, percent);
    (degrees+360<GPS8.heading()+360) ? Drivetrain.turn(left) : Drivetrain.turn(right);
    wait(1,msec);
  }
  Drivetrain.stop();
}

void goXtoY(double targetX, double targetY) {
  ((GPS8.xPosition(mm)-targetX)<0) ? myTurn(90) : myTurn(270);
  wait(0.5, seconds);
  Drivetrain.driveFor(targetX-GPS8.xPosition(mm), mm, true);
  wait(0.5, seconds);
  ((GPS8.yPosition(mm)-targetY)<0) ? myTurn(0) : myTurn(180);
  wait(0.5, seconds);
  Drivetrain.driveFor(targetX-GPS8.xPosition(mm), mm, true);
}

void goFrom(double startX, double startY, double targetX, double targetY) {
  double direction = ((atan2(targetX-startX, targetY-startY)*180/M_PI));
  myTurn(direction);
  double distance = sqrt(pow(targetX-startX, 2) + pow(targetY-startY, 2));
  Drivetrain.driveFor(distance, mm, true);
}

void atanGoTo(double targetX, double targetY) {
  double direction = ((atan2(targetX-GPS8.xPosition(mm), targetY-GPS8.yPosition(mm))*180/M_PI));
  myTurn(direction);
  double distance = sqrt(pow(targetX-GPS8.xPosition(mm), 2) + pow(targetY-GPS8.yPosition(mm), 2));
  Drivetrain.driveFor(distance, mm, true);
}

int printPosition() {
  // Print GPS position values to the V5 Brain
  while(true) {
    Brain.Screen.clearScreen();
    Brain.Screen.setCursor(1,1);

    Brain.Screen.print("X: %.2f", GPS8.xPosition(mm));
    Brain.Screen.print("  Y: %.2f", GPS8.yPosition(mm));
    Brain.Screen.newLine();
    Brain.Screen.print("GPS HEADING: %.2f",GPS8.heading());
    wait(50, msec);
  }
  return 0;
}

int main() {
  // Calibrate the GPS Sensor before starting
  GPS8.calibrate();
  while (GPS8.isCalibrating()) { wait(50, msec); }

  thread bruh = thread(printPosition);

  Drivetrain.setTurnVelocity(10, percent);

  goFrom(0,0,-1000,500);
  atanGoTo(1000, 1000);
  goXtoY(0,0);
}
```


## Config:
```cpp
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain  Brain;

// VEXcode device constructors
motor LeftMotor = motor(PORT1, ratio18_1, false);
motor RightMotor = motor(PORT3, ratio18_1, true);
inertial DrivetrainInertial = inertial(PORT3);
smartdrive Drivetrain = smartdrive(LeftMotor, RightMotor, DrivetrainInertial, 319.19, 320, 40, mm, 1);
motor ForkMotorGroupMotorA = motor(PORT2, ratio18_1, false);
motor ForkMotorGroupMotorB = motor(PORT9, ratio18_1, true);
motor_group ForkMotorGroup = motor_group(ForkMotorGroupMotorA, ForkMotorGroupMotorB);
gps GPS8 = gps(PORT10, 0.00, -240.00, mm, 180);
distance DistanceLeft = distance(PORT12);
distance DistanceRight = distance(PORT20);
optical Optical19 = optical(PORT19);
bumper BumperA = bumper(Brain.ThreeWirePort.A);

// VEXcode generated functions



/**
 * Used to initialize code/tasks/devices added using tools in VEXcode Pro.
 * 
 * This should be called at the start of your int main function.
 */
void vexcodeInit( void ) {
  Brain.Screen.print("Device initialization...");
  Brain.Screen.setCursor(2, 1);
  // calibrate the drivetrain Inertial
  wait(200, msec);
  DrivetrainInertial.calibrate();
  Brain.Screen.print("Calibrating Inertial for Drivetrain");
  // wait for the Inertial calibration process to finish
  while (DrivetrainInertial.isCalibrating()) {
    wait(25, msec);
  }
  // reset the screen now that the calibration is complete
  Brain.Screen.clearScreen();
  Brain.Screen.setCursor(1,1);
  wait(50, msec);
  Brain.Screen.clearScreen();
}
```
