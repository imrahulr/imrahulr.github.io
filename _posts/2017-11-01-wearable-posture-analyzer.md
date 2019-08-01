---
layout: post-right-sidebar
title:  "Wearable Posture Analyzer and Improvement System"
author: imrahulr
categories: [ Machine Learning, Signal Processing, Texas Instruments, Fitness, DST ]
image: assets/images/8.jpg
---

The system tracks user posture and employs a machine learning algorithm to provide suggestions to improve his/her posture.

This idea was presented at <a href="https://innovate.mygov.in/india-innovation-challenge-design-contest-2017/">DST & Texas Instruments India Innovation Challenge Design Contest 2017</a> anchored by NSRCEL, Indian Institute of Management, Bangalore (IIMB) and supported by MyGov. Our team reached the Quarter Finals of this contest wherein we built a prototype of the idea. This post provides a detailed report of the idea and it's implementation in hardware and software.

---

## Project Abstract

"Sit up straight!" "Don't slouch!" I'm sure we've all heard those admonishing words more than once from our mother when we were growing up. A survey carried out by Livestrong indicated that exercising in wrong posture is the main reason of majority of injuries which includes shoulder injuries, knee injuries, and shin splint including dislocation. About 85% of the Indian population suffers from back or neck pain at some point in their lives and is the second most common reason for visiting a doctor. Bad posture is also dangerous for athletes training with weights where excessive pressure on wrong joint and muscles causes serious injuries. IT professionals who work 12 hrs a day on computer usually develop wrong neck and spinal posture leading to variety of health issues. This temporary bad posture habits get translated to permanent deformation overtime, this cycle is what we intend to break. The product that we present will detect and analyze user posture and will help the user to consciously improve his posture. This will help users to learn good postural habits that will carry over into their whole life.

---

## Proposed Solution

The proposed solution can be divided into six main segments:
- Data extraction network
- Internal communication system
- External communication system
- Machine learning on Cloud
- Visual feedback
- Battery SoC

#### Data Extraction Network

The data extraction network consists of 6 wireless sensor modules in total. Each sensor module consists of accelerometer, gyroscope and Li ion battery. The sensor readings are processed by TIVA processor and the processed data is sent to the central unit CC2460 which further communicates with the phone via its built-in Bluetooth. The readings of these six sensors can accurately predict the activity and the position of the user. The sensor modules are comfortable wearable bands.

<p align="center">
<img src="{{ site.baseurl }}/assets/images/wearable/1.png" alt="Block Diagram of Sensor Band"/>
</p>

#### Internal Communication System

The internal communication between the numerous sensor modules and the central unit CC2640 is done by RF transmitter and receiver. Each sensor is associated with RF transmitter and the main sensor unit has a RF receiver. The main sensor unit will receive the data every 0.5 sec from each sensor. After the data from all the sensors has been assimilated in 1 cycle which is 3 seconds, the data from the main sensor unit is transmitted to the phone via Bluetooth module. This is a repetitive operation and will go on every 3 sec.

#### External Communication System

The external communication between the CC2640 and the phone is achieved via Bluetooth. The data will be sent from the main unit every 3 sec to the user’s phone. If in case the phone is not around, then the data will be written to the memory interfaced with the microcontroller. In order to save memory bandwidth, the data to be stored will be first pre-processed before storing, enabling the user the user to track his movements even without phone for a longer time.

<p align="center">
<img src="{{ site.baseurl }}/assets/images/wearable/2.png" alt="Block Diagram of Overall System"/>
</p>

#### Machine Learning on Cloud

- The data from the main sensor unit will be stored on the phone local storage in SQLite database. This data will be uploaded to cloud when the user has network access.
- The data will be passed to an already built deep neural network model, whose output will be accurately mapped to a position and activity. This mapped output will then be passed to user’s phone, where it will be displayed.
- For making the unit user specific, the calibration can be done on the cloud where the data readings along with position is passed to the deep neural network. By using multivariate classification, a model will be formed which on user verification can be used for subsequent position and activity classification.

#### Visual Feedback

The application will also display user the time he spends in a particular position, time based analysis on when he slouches. The application will also notify the user to correct his posture when required. Haptic feedback will also be provided when the wrong posture is detected.

#### Battery SoC determining System

The battery percentage of battery associated with each sensor will be displayed by SoC determination using battery gauge IC.

---

## Hardware and Software Implementation of Each Component

#### MPU-6050

The MPU-6050 devices combine a 3-axis gyroscope and a 3-axis accelerometer on the same silicon die. The orientation of this motion sensor will indicate the posture of the user. The position of the user can be detected with the combined usage of all the sensors. After detecting the position with help of motion sensor the posture of the user can be analysed. The accelerometer and gyroscope combination is able to recognise all the possible user postures. For precision tracking of both fast and slow motions, the parts feature a user-programmable gyroscope full-scale range of ±250, ±500, ±1000, and ±2000°/sec dps) and a user-programmable accelerometer full-scale range of ±2g, ±4g, ±8g, and ±16g.

<p align="center">
<img src="{{ site.baseurl }}/assets/images/wearable/3.png" alt="Plot of Accelerometer Readings"/>
</p>

#### NRF 24L01 Transceiver

The NRF24L01 is a highly integrated ultra-low power 2Mbps wireless transceiver IC. The NRF 24L01 is ideal choice because of its small size, fast data transmission rate and intermediate transmission range. The transceiver IC is be used to transmit the readings generated by motion sensors to central unit. All the sensors on 6 bands will be connected to the central units using NRF transceiver. The communications between all the transceivers will be done through polling method. 
We have used RF24 Network library for creating a network with one master node and many child
nodes.

<p align="center">
<img src="{{ site.baseurl }}/assets/images/wearable/4.jpg" alt="RF-24 Network Topology"/>
</p>

#### TI TIVA TM4C123G Launchpad

TI TIVA TM4C123G is main processing unit of each band. The MPU-6050 motion sensor and NRF 24L01 will be connected to the TIVA launchpad. As TIVA is a low cost ARM cortex based microcontroller the Kalman filter ran much faster compared to other platforms. The faster data processing also enabled faster and accurate readings. The accelerometer and gyroscope could be used with highest sampling rate with TIVA launchpad. The readings generated by motion sensors will be received by TIVA using I2C protocol. The data will be processed and sent to central unit using wireless protocol of NRF transceiver.

#### C2640 - SimpleLink Ultra-Low Power MCU for Bluetooth Low Energy

The TI Bluetooth enabled C2640 acts as central unit to the system. The data collected from all the bands is again processed in order to make it suitable for the bluetooth transmission protocol. After the processing of the data it will be sent to user mobile device for analytics purposes. As the microcontroller uses BLE the power consumption of central unit will be largely reduced.

#### Battery Gas Gauge - bq27421-G1

The bq27421-G1 battery fuel gauge accurately predicts the battery capacity and other operational characteristics of a single, Li-based, rechargeable cell. It can be interrogated by a system processor to provide cell information, such as state-of-charge (SOC). We will be using this IC to measure the SOC of the batteries used to power the bands.

---

## Expected Outcome

<p align="center">
<img src="{{ site.baseurl }}/assets/images/wearable/5.jpg" alt="Calculation of Trunk Posture"/>
</p>

**The calculation of the trunk posture change.** The trunk postural changes were calculated with the data of the sensor modules and was defined as the change of inclination differences between angles α(1,2,3) and β(1,2,3), respectively, which formed by the tangent and vertical line in the (a) sagittal and (b) coronal planes. This values were then classified into poor, neutral and good posture by our machine learning algorithm.

The aim of the system for posture training is to keep the slouching off as long as the user can, thus the users will learn which postures will keep their back in natural spinal curvature. Eventually, the users can be trained to maintain the natural spinal curvature (good posture) more often as their posture habit.

<p align="center">
<img src="{{ site.baseurl }}/assets/images/wearable/6.png" alt="Trunk Posture Deviation"/>
</p>

**The average trunk posture deviations in 4-day trunk posture monitoring.** The average trunk posture deviation from the neutral standing position of the subjects in 4-Day trial. Three line- segments represent the relative tilting of the 3 sensor modules to the neutral position. Dotted lines represent the posture in neutral position. The inter-segmental angles between 2 line- segments represent the trunk posture angles. (NF: without feedback, F: with feedback).

---

## Technical Challenges Faced

- Difficult in obtaining ICs.
- Problems with stable reading from analog sensors like accelerometer leading us to use Kalman filter.
- Obtaining an optimum position for sensor unit placement in order to correctly classify posture with minimum sensor units.
- Finding a suitable algorithm to classify the data and making sure the algorithm can generalize to a wide variety of data from different source and distribution.
- Difficulty in finding and operating multi transceiver wireless protocol.

---

## Non-Technical Challenges Faced

- Making the sensor bands as comfortable as possible for the user.
- Making the product with minimum price as possible.
- Our product being in a niche market, convincing people who have not heard of this niche to try our product.
- Reducing the size of sensor band to be as small as possible.
- Developing an enclosure for sensors which can handle rough and tough conditions.

---

## Things that Add Further Value

- Feedback system like beep and phone notification that can remind user to correct his slouching posture.
- Inform user about his posture of entire body by creating a easy to wear sensor network.
- Adding heart rate analyser, step counter to complement our product making it a overall fitness device.
- The system can be made more robust and user friendly by shifting to wireless technology.
- An hourly charged service of doctors can be provided which will analyse the users data and provide helpful insights, suggestions and warnings if any.

## Thank You!

---

## Important Links

<a href="https://innovate.mygov.in/india-innovation-challenge-design-contest-2017/">DST & Texas Instruments India Innovation Challenge Design Contest 2017</a><br>
<a href="https://www.bzarg.com/p/how-a-kalman-filter-works-in-pictures/">How a Kalman filter works?</a><br>
<a href="https://tmrh20.github.io/RF24Mesh/">RF24 Mesh Topology - GitHub</a><br>
<a href="https://howtomechatronics.com/tutorials/arduino arduino-and-mpu6050-accelerometer-and-gyroscope-tutorial/">MPU 6050 tutorial</a>