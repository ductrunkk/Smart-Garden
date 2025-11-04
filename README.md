# Smart Garden Project
## 1. 🎯 Project Introduction

  In the context of climate change, pollution, and busy urban lifestyles, this project constructs a fully automated "Smart Garden" model. The goal is to create an advanced technological solution to optimize plant care, conserve resources (water, electricity), and provide a green, modern living solution for urban spaces.

  The system is capable of monitoring environmental factors in real-time and automatically making decisions to control actuators (pumps, curtains, lights, air conditioners) to maintain ideal living conditions for plants.

## 2. 🏗️ System Architecture

The project is built on a two-component architecture, with the components communicating via a Serial (UART) port:

1.  **Embedded Controller (ESP32):**
    * Serves as the autonomous "brain" of the garden.
    * Utilizes a Real-Time Operating System (FreeRTOS) to handle multitasking robustly.
    * Responsible for reading data from all sensors and executing autonomous control logic.
    * Stores schedules and setting thresholds in EEPROM to operate independently, even if disconnected from the PC.

2.  **Monitoring Interface (PC - Qt/C++):**
    * Acts as the central control and monitoring dashboard.
    * Allows the user to track real-time parameters via charts and gauges.
    * Provides an interface for configuring automatic modes (scheduling, setting thresholds) and manual control.
    * Integrates a camera surveillance system (using OpenCV) with recording and playback functionalities.

## 3. ✨ Key Features

### Embedded (ESP32 & FreeRTOS)

* **Real-Time Multitasking:** Uses FreeRTOS with three main tasks running in parallel across 2 CPU cores (SensorTask, ControlTask, SerialTask) to ensure a responsive and stable system.
* **Automatic Control Logic:**
    * **Watering:** Automatically waters when soil moisture drops below a threshold, or follows a schedule (using RTC DS1307).
    * **Curtains:** Automatically opens/closes curtains based on light intensity thresholds (using DC motors and L298N).
    * **Climate:** Automatically toggles the air conditioner (Peltier) and fan based on room temperature thresholds.
    * **Lighting:** Automatically turns on/off lights when it gets dark (light below threshold).
* **Manual Override:** Accepts direct control commands from the GUI (e.g., `WATERING_ON`, `CURTAIN1_TOGGLE`) to prioritize user actions.
* **Persistent Storage:** Automatically saves watering schedules and all sensor thresholds to EEPROM memory.
* **Safety Mechanisms:**
    * Integrates a **Task Watchdog Timer (WDT)** to automatically reboot if the main control task hang.
    * Automatically reverts to "auto mode" if the connection to the Qt interface is lost for more than 10 seconds (`qtTimer`).

### Desktop Interface (Qt 6 & OpenCV)

* **Visual Dashboard:** Displays real-time sensor data (soil moisture, air humidity, temperature, light) using gauges (QRoundProgressBar) and hourly bar charts (`QChart`).
* **System Configuration:** A settings panel allows the user to:
    * Add/remove watering schedules (time of day, duration).
    * Set all min/max thresholds for soil, light, and temperature sensors.
* **Camera System (OpenCV):**
    * Displays a **Live Feed** directly from the camera.
    * Allows **Image Capture** (saving as `.jpg`).
    * Allows **Video Recording** (saving as `.mp4` using `cv::VideoWriter`).
    * Includes a **Video Player** to review recorded footage, complete with a timeline slider, play/pause, and rewind/fast-forward.
* **Smart Communication:**
    * Automatically scans and detects the ESP32's Serial (COM) port.
    * Automatically syncs the PC's time with the RTC (DS1307) upon connection.
    * Logs all incoming sensor data to a local `.txt` file.

## 4. 🛠️ Hardware & Libraries

### Hardware Requirements

* Microcontroller: **ESP32 Dev Module**
* Sensors:
    * Soil Moisture Sensor
    * Light Sensor (Photodiode)
    * Temperature & Humidity Sensor (DHT11)
* Time Module:
    * Real-Time Clock **RTC DS1307**
* Actuators:
    * **4-Channel Relay Module** (for Pump, Light, AC)
    * Peltier Cooler (Thermoelectric)
    * 5V/12V Cooling Fan
    * DC Gear Motor (for curtains)
    * **L298N** Motor Driver (defined in code)

### Software & Library Requirements

| Environment | Technology/Library |
| :--- | :--- |
| **Embedded (ESP32)** | C++, Arduino Framework, **FreeRTOS** (ESP-IDF) |
| | `RTClib` (by Adafruit) |
| | `DHT sensor library` (by Adafruit) |
| **Desktop (GUI)** | C++, **Qt 5** (Core, Widgets, Charts, SerialPort) |
| | **OpenCV** (for video processing) |
| **Build Tools** | **PlatformIO** (for ESP32) |
| | **CMake** or **qmake** (for Qt) |

## 5. 🚀 Installation and Usage

### Part 1: Flashing the ESP32 (Using PlatformIO)

1.  Install the [PlatformIO IDE](https://platformio.org/) extension.
2.  Clone this repository.
3.  Open the ESP32 project folder with VS Code.
4.  Open the `platformio.ini` file and ensure the following libraries are in your `lib_deps`:
    ```ini
    lib_deps =
        adafruit/RTClib
        adafruit/DHT sensor library
        adafruit/Adafruit Unified Sensor
    ```
5.  Connect your ESP32 to your computer via USB.
6.  Click the **"Upload"** button (arrow icon) on the PlatformIO toolbar.

### Part 2: Compiling the Qt GUI (Using Qt Creator or VS Code)

1.  Install the [Qt 5 SDK](https://www.qt.io/download) (Ensure you select the `Qt Charts` and `Qt Serial Port` modules during installation).
2.  Install the **OpenCV** library for your operating system (e.g., on Ubuntu: `sudo apt install libopencv-dev`).
3.  Install a C++ compiler (g++/clang) and `cmake`.
4.  Open the project (the `.pro` or `CMakeLists.txt` file) using Qt Creator or VS Code (with the C/C++ and CMake extensions).
5.  Configure the build kit.
6.  Click **"Build"** and then **"Run"**.

### Part 3: Operation

1.  Ensure the ESP32 is flashed and connected to the PC via USB.
2.  Run the compiled Qt GUI application.
3.  The application should automatically find the Serial port and connect. The time on the ESP32 will be synced.
4.  Use the dashboard to monitor, control manually, or navigate to the "Settings" tab to configure watering schedules and automatic thresholds.
