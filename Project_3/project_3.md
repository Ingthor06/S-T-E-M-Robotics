# Project 3

## Part 1
### Main:
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

  // The robot will stop driving when the Range Finder is less than 300 mm away
  // from an object.
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
