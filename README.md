# Obstacle_Avoiding_Robot

## Overview
This project is an Arduino-based obstacle-avoiding robot designed to detect and avoid obstacles solely. The robot uses an ultrasonic sensor to measure distance to objects in its path and makes real-time decisions to move forward, turn, or stop based on the detected distance.

I built this project to learn how sensors, motor drivers, and control logic work together in autonomous robotics. It helped me understand distance measurement, conditional logic, and motor control using a microcontroller.

## Motivation
I am interested in robotics and engineering. This project helped me improve my understanding of embedded systems, problem-solving, and hands-on design.

## Hardware Used
- Arduino Uno / Nano
- Motor Driver (L298N / L293D)
- DC Motors
- Servo Motor
- Sensors (Ultrasonic Sensor)
- Chassis & Wheels
- Battery Pack
- Jumper Wires

## Software & Tools 
- Arduino IDE
- Arduino C++
- Github

## How It Works
1. The Ultrasonic sensor produces sound waves that measure the distance between the bot and the obstacle. 
2. Arduino processes the sensor data.
3. If the measured distance is very close, then it moves backward, and the servo motor is powered, and checks which way it should turn, keeps moving forward, and it all repeats.  
4. The robot performs its task autonomously.

## Challenges & Solutions
- **Problem:**   Robot reacted too late to obstacles.                                                                                                                                     
  **Solution:** I reduced the robot’s speed and adjusted the minimum distance threshold so the robot had more time to detect and avoid obstacles.
- **Problem:** Ultrasonic sensor gave unstable readings.  
  **Solution:** Added delays and filtered noisy distance values.
- **Problem:**  Robot colliding with the obstacle.     
  **Solution:** Make sure that the obstacle is more than the height of the ultrasonic sensor.
  

## Improvements & Future Work
- Add speed control using PWM
- Improve accuracy and responsiveness
- Add more sensors or smarter logic

## Media
https://github.com/user-attachments/assets/ee80d2f5-4c8f-47db-8b3a-44508613616a    
https://github.com/user-attachments/assets/ef78c7ec-4640-45f1-8fd0-fbc44abfabcd   
https://github.com/user-attachments/assets/d38295d3-5425-43c7-b379-3d0e162a8858   
https://github.com/user-attachments/assets/f92f2486-382b-4538-b993-33319c7b9868     
//Please open these images in a new tab by pasting these links to get a reference.    
 
## What I Learned
- Sensor integration
- Motor control
- How does an Ultrasonic Sensor works
-  Understanding Arduino code
- Debugging hardware and software

## Author
Shrehit Dhiman  
Student interested in robotics and engineering 

## Code
```cpp

//Please subscribe to our YouTube channel for more projects https://www.youtube.com/c/Technicalromboz.  
//This is the source of this code, but explained by me.    


#include  <NewPing.h>        //Ultrasonic sensor function library. You must install this library
#include  <Servo.h>          //Servo motor library. This is the standard library. Please download this library.

//Arduino Pins
//in4 - 2
//in3 - 3
//in2 - 5
//in1 - 4


const  int LeftMotorForward = 2;
const int LeftMotorBackward = 3;
const int RightMotorForward  = 4;
const int RightMotorBackward = 5;

//sensor pins
#define trig_pin  A1 //analog input 1
#define echo_pin A2 //analog input 2

#define maximum_distance  200/
boolean goesForward = false; //it is to avoid motor glitches or unnecessary code execution.
int distance = 100;

NewPing sonar(trig_pin,  echo_pin, maximum_distance); //sensor function
Servo servo_motor; //our servo  name


void setup(){
// The input and output sets the usage so we can obtain the appropriate result that we wish for.

  pinMode(RightMotorForward, OUTPUT);
  pinMode(LeftMotorForward,  OUTPUT);
  pinMode(LeftMotorBackward, OUTPUT);
  pinMode(RightMotorBackward,  OUTPUT);
  
  servo_motor.attach(8); //our servo pin
//This is a sensor scanning routine — the robot knows how far obstacles are in a particular direction, so it can decide where to move next.
  servo_motor.write(115);
  delay(2000);
  distance = readPing();
  delay(100);
  distance = readPing();
  delay(100);
  distance = readPing();
  delay(100);
  distance = readPing();
  delay(100);
}

void loop(){
//set our variables
  int distanceRight = 0;
  int  distanceLeft = 0;
  delay(50);

  if (distance <= 20){ // it is to make sure that at a safe distance from the obstacle, it do these following steps
    moveStop();
    delay(300);
    moveBackward();
    delay(400);
    moveStop();
    delay(300);
    distanceRight = lookRight();
    delay(300);
    distanceLeft  = lookLeft();
    delay(300);
//The if and else statement help us decide which turn to make after the look up from the Ultrasonic sensor.
    if (distance >= distanceLeft){
      turnRight();
      moveStop();
    }
    else{
      turnLeft();
      moveStop();
    }
  }
  else{
    moveForward(); 
  }
    distance = readPing();
}
//Make sure to power the servo in the right direction.
int  lookRight(){  
  servo_motor.write(50);
  delay(500);
  int distance =  readPing();
  delay(100);
  servo_motor.write(115);
  return distance;
}
//Make sure to power the servo in the right direction.

int  lookLeft(){
  servo_motor.write(170);
  delay(500);
  int distance = readPing();
  delay(100);
  servo_motor.write(115);
  return distance;
  delay(100);
}
//This function asks the ultrasonic sensor for a distance measurement, waits a short time to ensure the reading is valid, replaces any failed readings (0) with a large number (250), and then returns the measured distance in centimeters.
int  readPing(){
  delay(70);
  int cm = sonar.ping_cm();
  if (cm==0){
    cm=250;
  }
  return cm;
}
// It prevents the motor driver from giving any power to the motors.
void moveStop(){
  
  digitalWrite(RightMotorForward,  LOW);
  digitalWrite(LeftMotorForward, LOW);
  digitalWrite(RightMotorBackward,  LOW);
  digitalWrite(LeftMotorBackward, LOW);
}
// It commands the driver to power the motor so it moves forward.
void moveForward(){

  if(!goesForward){

    goesForward=true;
    
    digitalWrite(LeftMotorForward,  HIGH);
    digitalWrite(RightMotorForward, HIGH);
  
    digitalWrite(LeftMotorBackward,  LOW);
    digitalWrite(RightMotorBackward, LOW); 
  }
}
// It commands the driver to power the motor so it moves backwards.

void moveBackward(){

  goesForward=false;

  digitalWrite(LeftMotorBackward, HIGH);
  digitalWrite(RightMotorBackward,  HIGH);
  
  digitalWrite(LeftMotorForward, LOW);
  digitalWrite(RightMotorForward,  LOW);
  
}
// It commands the driver to power the motor so it moves right.

void turnRight(){

  digitalWrite(LeftMotorForward,  HIGH);
  digitalWrite(RightMotorBackward, HIGH);
  
  digitalWrite(LeftMotorBackward,  LOW);
  digitalWrite(RightMotorForward, LOW);
  
  delay(500);
  
  digitalWrite(LeftMotorForward, HIGH);
  digitalWrite(RightMotorForward, HIGH);
  
  digitalWrite(LeftMotorBackward, LOW);
  digitalWrite(RightMotorBackward,  LOW);
 
  
  
}
// It commands the driver to power the motor so it moves left.

void turnLeft(){

  digitalWrite(LeftMotorBackward,  HIGH);
  digitalWrite(RightMotorForward, HIGH);
  
  digitalWrite(LeftMotorForward,  LOW);
  digitalWrite(RightMotorBackward, LOW);

  delay(500);
  
  digitalWrite(LeftMotorForward, HIGH);
  digitalWrite(RightMotorForward, HIGH);
  
  digitalWrite(LeftMotorBackward, LOW);
  digitalWrite(RightMotorBackward,  LOW);
}

