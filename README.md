
# LDF Notes

This code was forked from a public repo. The library supports several IMU chipsets. 

The two things you need that are not in the library are:
* install QTCreator in order to easily make the library. The link showing that is here: http://jetsonhacks.com/2015/04/22/install-qt-creator-on-nvidia-jetson-tk1/. I (Larry) also have years of QTCreator experience so I can help with that if necessary.
* Wire up the Adafruit IMU to the Jetson GPIO socket. The link showing that is here: http://jetsonhacks.com/2015/04/23/inertial-measurement-unit-imu-part-ii/. Note that the picture is actually wrong! and you have to pay attention to the pinout labels to do this right. We are using the 3.3v connection.
#


# RTIMULib - a versatile C++ and Python 9-dof, 10-dof and 11-dof IMU library

RTIMULib is the simplest way to connect a 9-dof, 10-dof or 11-dof IMU to an embedded Linux system and obtain quaternion or Euler angle pose data. Basically, two simple function calls (IMUInit() and IMURead()) are pretty much all that's needed to integrate RTIMULib.

## Please note that this library is no longer supported.

## Features

The Linux directory contains the main demo apps for embeeded Linux systems:

* RTIMULibDrive is a simple app that shows to to use the RTIMULib library in a basic way.
* RTIMULibDrive10 adds support for pressure/temperature sensors.
* RTIMULibDrive11 adds support for pressure/temperature/humidity sensors.
* RTIMULibCal is a command line calibration tool for the magnetometers and accelerometers.
* RTIMULibvrpn shows how to use RTIMULib with vrpn.
* RTIMULibDemo is a simple GUI app that displays the fused IMU data in real-time.
* RTIMULibDemoGL adds OpenGL visualization to RTIMULibDemo.

RTIMULib is a C++ library but there are also Python bindings in Linux/python. It's easy to build and install the Python RTIMULib library using the provided setup.py after which any Python script will have access to RTIMULib functionality. Two demo scripts show how to use the Python interface.

RTIMULib currently supports the following IMUs:

* InvenSense MPU-9150 single chip IMU.
* InvenSense MPU-6050 plus HMC5883 magnetometer on MPU-6050's aux bus (handled by the MPU-9150 driver).
* InvenSense MPU-6050 gyros + acclerometers. Treated as MPU-9150 without magnetometers.
* InvenSense MPU-9250 single chip IMU (I2C and SPI).
* STM LSM9DS0 single chip IMU.
* STM LSM9DS1 single chip IMU.
* L3GD20H + LSM303D (optionally with the LPS25H) as used on the Pololu AltIMU-10 v4.
* L3GD20 + LSM303DLHC as used on the Adafruit 9-dof (older version with GD20 gyro) IMU. 
* L3GD20H + LSM303DLHC (optionally with BMP180) as used on the new Adafruit 10-dof IMU.
* Bosch BMX055 (although magnetometer support is experimental currently).
* Bosch BNO055 IMU with onchip fusion. Note: will not work reliably with RaspberryPi/Pi2 due to clock-stretching issues.

The LSM9DS1 implementation was generously supplied by XECDesign.

Pressure/temperature sensing is supported for the following pressure sensors:

* BMP180
* LPS25H
* MS5611
* MS5637

Humidity/temperature sensing is supported for the following humidity sensors:

* HTS221
* HTU21D

The humidity infrastructure and HTS221 support was generously supplied by XECDesign. It follows the model used by the pressure infrastructure - see RTIMULibDrive11 for an example of how to use this.

Note that currently only pressure and humidity sensors connected via I2C are supported. Also, an MS5637 sensor will be auto-detected as an MS5611. To get the correct processing for the MS5637, edit the RTIMULib.ini file and set PressureType=5.

By default, RTIMULib will try to autodiscover IMUs, pressure and humidity sensors on I2C and SPI busses (only IMUs on the SPI bus). This will use I2C bus 1 and SPI bus 0 although this can be changed by hand editing the .ini settings file (usually called RTIMULib.ini) loaded/saved in the current working directory by any of the RTIMULib apps. RTIMULib.ini is self-documenting making it easy to edit. Alternatively, RTIMULibDemo and RTIMULibDemoGL provide a GUI interface for changing some of the major settings in the .ini file.

RTIMULib also supports multiple sensor integration fusion filters such as Kalman filters.

Two types of platforms are supported:

* Embedded Linux. RTIMULib is supported for the Raspberry Pi (Raspbian) and Intel Edison. Demo apps for these can be found in the Linux directory and instructions for building and running can be found there. Its prerequisites are very simple - just I2C support on the target system along with the standard build-essential (included in the Raspberry Pi Raspbian distribution by default).

* Desktop (Ubuntu/Windows/Mac). There are two apps (RTHostIMU and RTHostIMUGL) that allow the sensor fusion to be separated from the sensor interfacing and data collection. An Arduino (running the RTArduLinkIMU sketch from the RTIMULib-Arduino repo) fitted with an IMU chip collects the sensor data and sends it to the desktop. RTHostIMU and RTHostIMUGL (this one has an OpenGL visualization of the data) communicate with the Arduino via a USB connection.

The MPU-9250 and SPI driver code is based on code generously supplied by staslock@gmail.com (www.clickdrive.io). I am sure that any bugs that may exist are due to my integration efforts and not the quality of the supplied code!

RTIMULib is licensed under the MIT license.

## Repo structure

### RTIMULib

This is the actual RTIMULib library source. Custom apps only need to include this library.

### Linux

This directory contains the embedded Linux demo apps (for Raspberry Pi and Intel Edison) and also the Python interface to RTIMULib.

### RTHost

RTHost contains the two apps, RTHost and RTHostGL, that can be used by desktops that don't have direct connection to an IMU (as they don't have I2C or SPI interfaces). An Arduino running RTArduLinkIMU from the RTIMULib-Arduino repo provides the hardware interface and a USB cable provides the connection between the desktop and the Arduino.

### RTEllipsoidFit

This contains Octave code used by the ellipsiod fit data generation in RTIMULibCal, RTIMULibDemo, RTIMULibDemoGL, RTHostIMU and RTHostIMUGL. It's important that a copy of this directory is at the same level, or the one above, the app's working directory or ellipsoid fit data generation will fail.

## Note about magnetometer (compass) calibration

It is essential to calibrate the magnetometer or else very poor fusion results will be obtained. RTIMULibDemo (GUI) and RTIMULibCal (command line) can be used to do this. They both support magnetometer min/max, magnetometer ellipsoid fit and accelerometer min/max calibration.

Also, if using a non-standard axis rotation magnetometer calibration (and accelerometer calibration if that has been performed) MUST be run AFTER changing the axis rotation.

