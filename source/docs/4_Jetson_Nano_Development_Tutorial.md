# 4. Jetson Nano Development Tutorial

<img class="common_img" src="../_static/media/chapter_1/section_4/media/image3.png" style="width:500px" />

## 4.1 Getting Started

### 4.1.1 Wiring Instruction

When wiring the ultrasonic module, connect the positive and negative pins of the module to the pins of the Raspberry Pi. Connect the TRIG pin to GPIO22 and the ECHO pin to GPIO24.

<img class="common_img" src="../_static/media/chapter_1/section_4/media/image4.png" style="width:600px" />

> [!NOTE]
>
> **Before powering on, ensure that no metal objects are touching the controller. Otherwise, the exposed pins at the bottom of the board may cause a short circuit and damage the controller.**

### 4.1.2 Environment Configuration

Install NoMachine on your computer. The software package is located under "**[Appendix-> Remote Desktop Connection Tool](https://drive.google.com/drive/folders/1ITS4OwvKXYVtSplOqO7kgig-IejRPSEn?usp=sharing)**". For the detailed operations of NoMachine, please refer to the same directory.

Drag the program and SDK library files into the Raspberry Pi system image. For demonstration purposes, the files are placed on the Desktop in this example.

## 4.2 Test Case

Program to display the ultrasonic sensor status in the Jetson system terminal window.

### 4.2.1 Program Download

1. Open the terminal and enter the following command to navigate to the program directory, and press Enter

```bash
cd Desktop/
```

2. Run the program by entering:

```bash
python3 UltrasonicSersorDemo.py 
```

### 4.2.2 Project Outcome

Place obstacles at varying distances in front of the ultrasonic sensor. The terminal interface will display the distances detected by the sensor in real time.

<img class="common_img" src="../_static/media/chapter_1/section_4/media/image7.png" style="width:500px" />

### 4.2.3 Program Brief Analysis

- **Import Libraries**

```py
import Jetson.GPIO as GPIO
import time
```

Import the Raspberry Pi **GPIO** library and the **time** library for handling timing.

- **Initialization Sequence**

```py
# Set GPIO pin numbers (设置GPIO针脚编号)
TRIG = 10
ECHO = 6

# Set GPIO mode to BCM (设置GPIO模式为BCM)
GPIO.setmode(GPIO.BCM)

# Set TRIG pin as output, ECHO pin as input (设置TRIG针脚为输出, ECHO针脚为输入)
GPIO.setup(TRIG, GPIO.OUT)
GPIO.setup(ECHO, GPIO.IN)
```

Initialize the relevant GPIO pins, set their modes, and print their status.

`TRIG = 10`: Sets the TRIG pin number to 10.

`ECHO = 6`: Sets the ECHO pin number to 6.

`GPIO.setmode(GPIO.BCM)`: Sets the GPIO numbering mode to BCM numbering.

`GPIO.setup(TRIG, GPIO.OUT)`: Configures the TRIG pin as an output.

`GPIO.setup(ECHO, GPIO.IN)`: Configures the ECHO pin as an input.

- **Emit an Ultrasonic Pulse**

```py
def distance_measurement():
    # Send ultrasonic pulse (发送超声波脉冲)
    GPIO.output(TRIG, True)
    time.sleep(0.00002)
    GPIO.output(TRIG, False)

    start_time = time.time()
    stop_time = time.time()

    # Record the time of ultrasonic emission and reception (记录超声波发射和接收的时间)
    while GPIO.input(ECHO) == 0:
        start_time = time.time()

    while GPIO.input(ECHO) == 1:
        stop_time = time.time()
```

Trigger the ultrasonic sensor by setting the TRIG pin to high, pausing briefly, and then setting it to low. Next, record the time when the ultrasonic pulse is sent and when it is received using variables. Timing stops when the ECHO pin detects the returning pulse.

- **Calculating Distance**

```py
# Calculate distance (计算距离)
    elapsed_time = stop_time - start_time
    distance = (elapsed_time * 34300) / 2  # 34300 is the speed of sound, unit is cm/s (34300为声速，单位是厘米/秒)

    return distance
```

Use the function to calculate the ultrasonic pulse's round-trip time, then multiply by half the speed of sound, 34300 cm/s, to determine the distance.

- **Print Data**

```py
try:
    while True:
        dist = distance_measurement()
        print(f"距离: {dist:.2f}厘米")
        time.sleep(0.1)
```

Call the `distance_measurement` function to obtain the measured distance, then print the value rounded to two decimal places.