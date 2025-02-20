# Project 4

## Part 1
### SUDUKODI 
```
bothmotors.setvelocity(50%)

int threshold = 30


while(true) {
    if (linefound) {
        bothwheels.spin()
    } else {
        bothwheels.stop()
    }

    int leftmotorvelocity = 50
    int rightmotorvelocity = 50

    if (leftsensor.value() >= threshold && rightsensor.value() < threshold) {
        leftmotorvelocity -= 20
        if centersensor.value() > threshold {
            leftmotorvelocity += 10
        }
    }
    if (rightsensor.value() >= threshold && leftsensor.value() < threshold) {
        rightmotorvelocity -= 20
        if centersensor.value() > threshold {
            rightmotorvelocity += 10
        }
    }
    leftmotor.setvelocity(leftmotorvelocity, percent)
    rightmotor.setvelocity(rightmotorvelocity, percent)
}
```

### Main:
[Educational Video](https://youtu.be/dP-LQtYy-pw)
```cpp
#include "vex.h"

using namespace vex;

int threshold;

int status = 0;

int last;

int PrintOnScreen() {
  while(true) {
    Brain.Screen.clearLine(1);
    Brain.Screen.setCursor(1,1);
    switch (status) {
      case 0: // engin lina
        Brain.Screen.print("Leita af linu...");
        break;
      case 1: // line til vinstri
        Brain.Screen.print("Lina til vinstri...");
        break;
      case 2: // lina i midju
        Brain.Screen.print("Lina i midjunni...");
        break;
      case 3: // lina til haegri
        Brain.Screen.print("Lina til haegri...");
        break;
    }
    wait(5, msec);
  }
  return 0;
}

int main() {
  // Initializing Robot Configuration. DO NOT REMOVE!
  vexcodeInit();

  thread screen = thread(PrintOnScreen);

  threshold = 30;
  
  int leftmotorvelocity;
  int rightmotorvelocity;

  while(true) {

    int L = 100-LineTrackerL.reflectivity(); //snua gildinu vid t.d. 75% verdur 25%
    int C = 100-LineTrackerC.reflectivity();
    int R = 100-LineTrackerR.reflectivity();
    
    leftmotorvelocity = 15;
    rightmotorvelocity = 15;

    status = 0;

    if (L <= 100-threshold || C <= 100-threshold || R <= 100-threshold) { //keyrir ef bilinn er a golfinu
      status = 2;
      LeftMotor.spin(forward);
      RightMotor.spin(forward);
    } else { RightMotor.stop(); LeftMotor.stop(); last = 0;} //stoppar bilinn ef honum er lift up og hreynsar minnid
    
    if (last==1) { leftmotorvelocity = 5; status = 1;} // segir honum hvad a ad gera eftir minninu
    if (last==2) { rightmotorvelocity = 5; status = 3; }

    if (L >= R+threshold) {
        last = 1;
        leftmotorvelocity = 8;
    }
    if (R >= L+threshold) {
        last = 2;
        rightmotorvelocity = 8;
    }

    LeftMotor.setVelocity(leftmotorvelocity, percent);
    RightMotor.setVelocity(rightmotorvelocity, percent);
    wait(10, msec);
  }
}
```
[](https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExem8ybXFtYWl1Z3MyaGxodXJsNzk5Nno1dTZ3Z2Zoa21xMWZpdmUwOSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/iglIUsXtZHwMWOMvfI/giphy.gif)

## Config:
```cpp
#include "vex.h"

using namespace vex;
using signature = vision::signature;
using code = vision::code;

// A global instance of brain used for printing to the V5 Brain screen
brain Brain;

// VEXcode device constructors
line LineTrackerL = line(Brain.ThreeWirePort.E);
line LineTrackerC = line(Brain.ThreeWirePort.F);
line LineTrackerR = line(Brain.ThreeWirePort.G);
motor LeftMotor = motor(PORT1, ratio18_1, false);
motor RightMotor = motor(PORT3, ratio18_1, true);

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
