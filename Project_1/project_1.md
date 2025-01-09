# Project 1

## Codes:
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
