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

[Testrun video](https://youtu.be/P1BteJjgTnA)

### Main:
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
