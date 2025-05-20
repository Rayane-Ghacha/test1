# WRO Future Engineers Robot Project Documentation

## Project Overview

This project involves a robot that detects colors via a camera connected to a Raspberry Pi, processes the data, and sends control signals to an Arduino which drives the motors. A dashboard on the Raspberry Pi previews the state of the robot’s components, allowing real-time monitoring and control.

## Hardware Setup

### Arduino Connections

| Component           | Arduino Pin |
|---------------------|-------------|
| Motor Speed Control | ENA (Pin 11)|
| Motor Direction 1   | IN_1 (Pin 13) |
| Motor Direction 2   | IN_2 (Pin 12) |
| Steering Servo      | Pin 10 |
| Encoder A           | Pin 2 (Interrupt Pin) |
| Encoder B           | Pin 3 (Interrupt Pin) |
| Buzzer              | Pin 8 |
| Switch              | Pin A0 |
| VL53L1X Sensors     | XSHUT Pins (Pins 4, 5, 6, 7) |

```cpp
#define ENA  11                
#define IN_1  13               
#define IN_2  12       
#define buzzerPin  8      
#define STEERING_SERVO_PIN 10 
#define ENCODER_PIN_A 2       
#define ENCODER_PIN_B 3   
#define switchPin  A0        
#define xshutPins[sensorCount] = { 4, 5, 6, 7 };
```

**Note**: Ensure that the **VL53L1X** sensors are correctly wired for I2C communication, and each sensor is assigned a unique address in the `setupSensors()` function.

### Raspberry Pi Connections

The Raspberry Pi is connected to the Arduino via USB or direct TX/RX serial pins, enabling it to send color detection data to control the robot’s motors. A PS4 controller is used for manual control, where:

- **Left Joystick**: Controls forward/backward movement.
- **Right Joystick**: Controls steering.

## Software Requirements

### Python Libraries (Raspberry Pi)

Ensure the following Python libraries are installed on the Raspberry Pi:

```bash
pip3 install numpy tkinter matplotlib pyserial pygame opencv-python
```

| Library             | Purpose |
|---------------------|-------------|
| `numpy` | For numerical operations and array handling |
| `tkinter` | For creating GUI applications |
| `matplotlib` | For plotting data and visualizations |
| `pyserial` | Serial communication between Pi and Arduino|
| `pygame` | For handling joystick input and sound |
| `opencv-python`   | Color detection and camera processing |
| `RPi.GPIO` or `gpiozero`   | GPIO control on Raspberry Pi |
	
Use `pip list` to verify installations.

### Arduino Libraries (Arduino IDE)
In the Arduino IDE, make sure to install the following libraries:
1. **Servo** - For controlling the steering servo.
2. **VL53L1X** - For handling ToF distance sensors.
3. **Wire** - For I2C communication.
```cpp
#include <Servo.h>    
#include <VL53L1X.h>  
#include <Wire.h>
```
   
To install these libraries:
1. Go to __Sketch__ > __Include Library__ > __Manage Libraries__….
2. Search for each library by name and install it.

## Communication Protocol
### Serial Communication
- The Raspberry Pi sends control data (speed and steering) based on color detection.
- The Arduino receives this data to control motor speed and direction.
- Arduino sends sensor data (distance, encoder count, etc.) back to the Raspberry Pi for dashboard updates.

### Data Format
- Data Sent to Arduino: `speed,steering\n` (e.g., `50,20\n`).
- Data Sent to Raspberry Pi: `frontDist,rightDist,leftDist,backDist,gyroZ,encoderSpeed\n` (e.g., `100,200,150,300,512,50.5\n`).
  
## Arduino Code Highlights
### Control Functions
1. `driveDistanceSpeed()` - Moves the robot a specified distance at a given speed.
2. `controlRobot()` - Controls motor speed and direction based on received data.
3. `sendDataToPi()` - Sends sensor readings to Raspberry Pi.
4. `receiveDataFromPi()` - Receives control commands from Raspberry Pi.
   
### Buzzer Functions
- `BuzzerRobotStart()` - Plays a start-up sound.
- `BuzzerRobotEnd()` - Plays a shutdown sound.
- `BuzzerRobotError()` - Indicates errors.
- `BuzzerRobotSpecial()` - Plays a special tune.

### Sensor Functions
- `setupSensors()` - Initializes and assigns addresses to each VL53L1X sensor.
- `readAndPrintDistances()` - Reads and prints distances from each sensor.
