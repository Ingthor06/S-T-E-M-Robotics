# Project 2

### Íslenska:
Öll keyrsla á bílnum er strjórnað með einungis vinstri rofanum, bæði til að keyra beint fram og after og til að beygja.
Til þess að stjórna krananum eru notaður L1 til að fara upp og L2 til að fara niður og svo nota R1 til að opna klónna og R2 til að loka henni.

### English:
All driving of the car is controlled with the left switch only, both for driving straight forward and backward and for turning. To control the crane, use L1 to go up and L2 to go down, then use R1 to open the claws and R2 to close them.

## Main:
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
