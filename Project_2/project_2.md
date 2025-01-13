# Project 2

Öll keyrsla á bílnum er strjórnað með einungis vinstri rofanum, bæði til að keyra beint fram og after og til að beygja.
Til þess að stjórna krananum eru notaður L1 til að fara upp og L2 til að fara niður og svo nota R1 til að opna klónna og R2 til að loka henni.

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
