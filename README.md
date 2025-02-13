<div align="center">
  <a href="https://git.io/typing-svg" target="_blank">
    <img src="https://readme-typing-svg.demolab.com?font=Kode+Mono&size=40&duration=2000&pause=1000&center=true&vCenter=true&width=435&lines=S-T-E-M+%26+Robotics" alt="Typing SVG: STEM & Robotics">
  </a>
</div>


![Taekniskolinn](https://img.shields.io/badge/Taekniskolinn-blue?style=for-the-badge&logo=https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcQqTwAaMeKxCyPqZWQFVSrB3ifYAiyyDOn1HQ&s&logoColor=white)

![GitHub stars](https://img.shields.io/github/stars/Ingthor06/S-T-E-M-Robotics?style=social)
![GitHub forks](https://img.shields.io/github/forks/Ingthor06/S-T-E-M-Robotics?style=social)
![Viewers](https://img.shields.io/badge/viewers-0-%23000000?style=flat-square&logo=GitHub&logoColor=white)
![GitHub contributors](https://img.shields.io/github/contributors/Ingthor06/S-T-E-M-Robotics)
![License](https://img.shields.io/github/license/Ingthor06/S-T-E-M-Robotics)


![C++](https://img.shields.io/badge/C%2B%2B-11-blue?logo=c%2B%2B&logoColor=white)


# Description:
### Íslenska:
Bekkjaverkefni sem kannar **VTVS** *(Vísindi, Tækni, Verkfræði og Stærðfræði)* í gegnum hönnun á vélmennum. Þetta geymsla inniheldur nýstárlegar hugmyndir, lausnaleit og samvinnu við smíði hagnýtra vélmenna. Verkefnið sameinar forritun, verkfræði og sköpun til að sýna raunhæf notkunarsvið vélmenna í STEM greinum.

### English:
A class project exploring **STEM** *(Science, Technology, Engineering, and Mathematics)* through robotics design. This repository features innovative ideas, problem-solving, and teamwork in creating functional robots. It combines coding, engineering, and creativity to showcase real-world applications of robotics in STEM fields.

> [!NOTE]
> This project is developed and designed by students in Tækniskólinn Iceland. I am working with both hardware and software in this project, combining physical components with programming to achieve the desired functionality. This project is for educational purposes only.

## Contributors:
+ **Developed by:** @vValdimar, @Ingthor06<br/>

  * 🔨 HARDWARE: *VEX V5* Robotics hardware is a system of motors, sensors, and components for building and competitive settings.
    
  * 🧑‍💻 SOFTWARE: The software engineering is used in C++ and enables robot control through VEXcode V5 using graphical or text-based programming.

## Project 1
### Overview:
In [Project 1](Project_1/project_1.md), we focused on programming a VEX Robotics robot to execute a series of movements and solve a specific path-following challenge. The following tasks were completed:

+ Forward and Reverse Movements: Programmed the robot to drive forward and backward for precise distances.

+ Accuracy in Positioning: Ensured that the robot remained within a 20 cm tolerance from its starting point after completing each sequence of movements.

+ Gyro Integration: Added a gyro sensor to the robot's hardware configuration, enabling accurate turns and enhanced control.

+ Fixed Turning Directions: Corrected the turning behavior by adjusting motor configurations, ensuring the robot turns as expected.

+ Path Following: Coded the robot to autonomously follow a difficult, predefined path with accuracy using the drivetrain and sensors.

  
These improvements were made to enhance the robot's ability to execute precise movements and solve complex tasks autonomously.



## Project 2
### Overview:
In [Project 2](Project_2/project_2.md), we focused on programming the functionality of the VEX V5 robot using a wireless controller. The main objectives were:

+ Wireless Controller:
We implemented functionality for the wireless controller, where each button was assigned a specific action, ensuring smooth connectivity between the controller and the robot.

+ Emergency Stop:
We created an emergency stop mechanism by mapping the bumper on the V5 robot to stop all movements instantly.
Additionally, pressing the 'X' button on the wireless controller also triggers the emergency stop, ensuring safety during operation.

+ Gyro Sensor for Circular Movement:
We integrated the gyro sensor to detect 360-degree rotations when the robot drove in a circle.
After completing the first 360-degree circle, the robot moved to a larger circle, progressing by increasing the circle size each time it completed a full rotation.

The robot continued this pattern until it had completed three progressively larger circles.

<p align="left">
  <img src="https://github.com/user-attachments/assets/5bbccaa3-77fa-4755-bc0a-00cec236c157" width="200" />
</p>



## Project 3: Obstacle Avoidance and Vision Tracking
In [Project 3](Project_3/project_3.md), we programmed a VEX V5 robot for autonomous navigation and object tracking. Using an ultrasonic sensor, the robot detected and avoided obstacles. We also connected to the Vision Sensor via Wi-Fi, allowing real-time access to the camera feed. Additionally, we implemented color-based object tracking, where the robot successfully followed a green cube in real-time.


+ Ultrasonic Sensor: Used for obstacle detection and collision avoidance. It continuously measured the distance between the robot and nearby objects, allowing it to stop or change direction when an object was     detected within a certain range. 

+ Vision Sensor Connection: The VEX V5 Brain generated a Wi-Fi network, which we connected to using our phones. By accessing the Vision Sensor through a web browser, we were able to view the live camera feed in   real-time.

+ Object & Color Tracking: We programmed the Vision Sensor to recognize and track a specific color, enabling the robot to follow an object of our choice. In this test, we selected a green cube, and the robot      successfully identified, tracked, and followed it as it moved. 

<p align="center">
  <img src="https://media2.giphy.com/media/v1.Y2lkPTc5MGI3NjExc216ZmY3N2c5Zml0eXJzamlnc2tucHNyYm82Z3drZXVycmRna2tndCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/NcECxf2Rurrq2VL0a9/giphy.gif" width="600" />
</p>
