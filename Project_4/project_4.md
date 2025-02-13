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
