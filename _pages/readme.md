---
layout: page
title: readme
---

# HONDA HARU Motor Module Control Program C++ and Python (External Version)

<!-- TOC start -->

- [Introduction](#introduction)
- [Test Machine Details](#test-machine-details)
- [Initial Steps](#initial-steps)
- [Running the Programs](#running-the-programs)
  * [Basic Setup](#basic-setup)
  * [Testing the motors](#testing-the-motors)
  * [Runing the Joystick Program](#runing-the-joystick-program)
- [Structure of the Main Program](#structure-of-the-main-program)
- [Providing Custom Inputs Without the Gamepad](#providing-custom-inputs-without-the-gamepad)
- [Time Profiling of the Program With the Current Hardware and Software](#time-profiling-of-the-program-with-the-current-hardware-and-software)

<!-- TOC end -->

## Introduction

This program has been developed to control the KTH neck module of the Honda HARU. The details of the test machine and on running the program are given below.

## Test Machine Details
1. **Processor**: Intel Core i5-4300 CPU @ 1.90 GHz (2.50 GHz Peak)
2. **RAM**: DDR3 16.0 GB
3. **Disk space**: 80.12 GB
4. **OS**: Ubuntu 22.04 full (normal) installation
5. **C++ version**: 11.3.0
6. **Python version**: 3.10.6

## Initial Steps
1. Run the following code
    ```
    sudo apt-get update
    sudo apt-get upgrade
    ```
2. Download and install Dynamixel Wizard following instructions given on the Robotis website [(installations and download link)](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_wizard2/#install-linux)
3. Download the Dynamixel SDK from the Robotis website [(download link)](https://github.com/ROBOTIS-GIT/DynamixelSDK/archive/refs/heads/master.zip)
4. Unzip the downloaded Dynamixel SDK into a folder of your choice
5. Follow the installation instructions for the Dynamixel SDK corresponding to C++ Linux [(installation instructions)](https://emanual.robotis.com/docs/en/software/dynamixel/dynamixel_sdk/library_setup/cpp_linux/#cpp-linux)
6. IMPORTANT!! Before running the test program disconnect the motor from any mechanical assemblies!!! (the motor with ID 1 needs to be free to rotate for the default example)
7. Additionally, change the buadrate and dynamixel ID in the `.cpp` file before running the test program. You might also have to also edit the control table addresses depending on the motor
8. If you would like to use an Xbox 360 controller, follow the instructions given on this page, [link](https://askubuntu.com/questions/14849/how-to-use-xbox-360-wireless-gaming-controller)
9. 5. To, check if the Xbox controller works, run
    ```
    sudo apt-get update
    sudo apt-get install jstest-gtk
    ```
10. Install the latest version of Python 3
11. Install the setserial module, by running
    ```
    sudo apt-get install setserial
    ```
12. Install Gnome Terminal to run multiple parallel instances of terminal
    ```
    sudo apt-get install gnome-terminal
    ```
    
## Running the Programs
### Basic Setup
1. Download this program as a .zip file and extract it into a folder of your choice
2. Ensure that the IDs of your motors are set according to the figure given below and change the baud rate for the motors to 4000000 (4M) on Dynamixel Wizard
    ![](Images/motor_ids.jpg)

### Testing the motors
1. Ensure that the motor IDs and baud rates match the figure above
2. Ensure that the port is correctly set in this file (#define DEVICENAME)
3. Disconenct the motors from all loads (wire ropes included)
4. Set all motors to a set poisition of 0 on the dynamixel wizard
5. Check if all motors are rotating synchronously
6. To terminate, hit Ctrl+C on terminal

### Runing the Joystick Program
1. Connect an Xbox 360 controller
2. Run `jstest-gtk` from the menu and test the controller
3. Note: The following programs work only on AMD64 architecture and a 64bit OS and assumes that the motors and U2D2 are connected on `/dev/ttyUSB0`
4. Navigate to program folder of your choice, for example `[KTH HARU C++ Directory]/Programs/HARU Program Sync Read Write`
5. Open terminal in this folder
6. Run the bash script to run the test program
    ```
    ./run_haru_sync_read_write.sh
    ```
7. The script will spawn a new terminal window and request the root password
8. The robot can now be controlled using the right joystick of the gamepad
9. Incase of any errors, disconnect and reconnect the gamepad and all connected USB devices
10. Repeat the instructions from the start

## Structure of the Main Program
1. The program consists of 3 parts, the wrapper bash script, the C++ program to communicate with the motors, and the Python program to read from the gamepad
2. The bash script provides a wrapper to concurrently execute the phython and C++ programs
3. A short overview of the bash script
    - The bash script executes the Python program on a seperate termial instance with root previlages
    - Next, the script sets the latency timer of `/dev/ttyUSB0` to 1ms (this is the minimum value)
    - The bash sript then compiles the C++ program and runs it on the current terminal instance
4. The Python program communicates with the C++ program through a memory mapped file

## Providing Custom Inputs Without the Gamepad
1. In order to provide custom inputs to the program without using a gamepad, the memory mapped file has to be updated by a feedeer program
2. The memory mapped file is a binary file named `data_exchange_file.bin` and contains two IEEE 754 float values expressed as binary values
3. The first float value refers to roll movement of the robot expressed in the range [-1, +1], the second float value refers to the pitch movement expressed in the range [-1, +1]
4. In order to provide inputs to this memory mapped file, you will need to create a program that accesses the memory mapped file with write previlages

## Time Profiling of the Program With the Current Hardware and Software
1. Note: The program works as intended only whe the USB latency is set to 1ms. The default is 16ms, and this slows the communication to a large extent.
2. To check if the USB latency, run,
    ```
    cat /sys/bus/usb-serial/devices/ttyUSB0/latency_timer
    ```
3. HARU Program With Classic Read Write (1 write and 1 read each for 4 motors): best case execution time approximately 10ms
4. HARU Program With Sync Read Write (1 write and 5 reads each for 4 motors): best case execution time approximately 6ms
5. The times are approximate and can sometimes take longer if the OS doesn't allocate processor time accordingly
