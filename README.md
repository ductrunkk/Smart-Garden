# Smart Garden Project
## 1. Project Introduction

  This project constructs an automated "Smart Garden" model. The goal is to create an advanced technological solution to optimize plant care, conserve resources (water, electricity), and provide a green, modern living solution for urban spaces.

  The system is capable of monitoring environmental factors in real-time and automatically making decisions to control actuators (pumps, curtains, lights, air conditioners) to maintain ideal living conditions for plants.

## 2. System Architecture

The project is built on a two-component architecture, with the components communicating via a Serial (UART) port:

1.  **Embedded Controller (ESP32):**
    * Serves as the autonomous "brain" of the garden.
    * Utilizes a Real-Time Operating System (FreeRTOS) to handle multitasking robustly.
    * Responsible for reading data from all sensors and executing autonomous control logic.
    * Stores schedules and setting thresholds in EEPROM to operate independently, even if disconnected from the PC.

2.  **Monitoring Interface (PC - Qt/C++):**
    * Acts as the central control and monitoring dashboard.
    * Allows the user to track real-time parameters via charts and gauges.
    * Integrates a camera surveillance system (using OpenCV) with recording and playback functionalities.

## 3. Key Features

### Embedded (ESP32 & FreeRTOS)

* **Real-Time Multitasking:** Uses FreeRTOS with three main tasks running in parallel across 2 CPU cores (SensorTask, ControlTask, SerialTask) to ensure a responsive and stable system.
* **Automatic Control Logic:**
    * **Watering:** Automatically waters when soil moisture drops below a threshold, or follows a schedule (using RTC DS1307).
    * **Curtains:** Automatically opens/closes curtains based on light intensity thresholds (using DC motors and L298N).
    * **Climate:** Automatically toggles the air conditioner (Peltier) and fan based on room temperature thresholds.
    * **Lighting:** Automatically turns on/off lights when it gets dark (light below threshold).
* **Manual Override:** Accepts direct control commands from the GUI to prioritize user actions.
* **Persistent Storage:** Automatically saves watering schedules and all sensor thresholds to EEPROM memory.

### Desktop Interface (Qt 6 & OpenCV)

* **Visual Dashboard:** Displays real-time sensor data (soil moisture, air humidity, temperature, light) using gauges and hourly bar charts.
* **System Configuration:** A settings panel allows the user to:
    * Add/remove watering schedules (time of day, duration).
    * Set all min/max thresholds for soil, light, and temperature sensors.
* **Camera System (OpenCV):**
    * Displays a **Live Feed** directly from the camera.
    * Allows **Image Capture** (saving as `.jpg`).
    * Allows **Video Recording** (saving as `.mp4` using `cv::VideoWriter`).
    * Includes a **Video Player** to review recorded footage, complete with a timeline slider, play/pause, and rewind/fast-forward.
