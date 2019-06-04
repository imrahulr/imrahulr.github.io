---
layout: post-right-sidebar
title:  "Predictive Maintenance for Digital Factory Automation"
author: imrahulr
categories: [ LSTM, Kalman Filter, Siemens MakeIT Real Hackathon ]
image: assets/images/7.png
---

This project was presented at <a href="https://www.hackerearth.com/challenges/hackathon/makeitreal/">Siemens MakeIT Real Hackathon 2017</a> hosted by Siemens at Bangalore. The idea was awarded 4th prize among all India participants. The code can be found in <a href="https://github.com/imrahulr/Pred-Maintenance-Siemens">this</a> GitHub repository.

---

## Problem Statement

Industrial plants increasingly make use of preventive maintenance to minimize downtime and thereby increase efficiency. Develop a system that identifies likelihood of failure of rotating equipment (for example motors, turbines) based on a stream of sensor data. This can be done by using open source datasets or by simulating the sensor data.

---

## Our Solution

In order to solve this problem, we built an end-to-end predictive maintenance system which collects data from sensors, uploads the data to a database, generates predictions regarding RUL of machines and notifies the results on a dashboard. The sensors monitor different parameters such as temperature, pressure and acceleration of different machine parts in order to keep a check on the performance of the rotary machine. At the server side, this data is used is used to train a machine learning model. This model predicts the remaining useful life (RUL) i.e., time to failure of the machine. These predictions are used to prevent failure of the machines by performing maintenance at appropriate time. We have also incorporated Fog computing which enables the system to take critical, urgent decisions with low time overhead.

The system monitors the condition of machines in order to estimate when maintenance should be performed. This allows convenient scheduling of corrective maintenance, and prevents unexpected machine failures by providing "the right information in the right time". By knowing which machine needs maintenance, maintenance work can be better planned and what would have been "unplanned stops" are transformed to shorter and fewer "planned stops", thus increasing plant availability. Other potential advantages include increased equipment lifetime, increased plant safety, fewer accidents with negative impact on environment, and optimized spare parts handling. Thus, this approach promises cost savings over routine or time-based preventive maintenance, because tasks are performed only when warranted. 

---

## Machine Learning for Predictive Maintenance

The subsequent sections of the post focus on the machine learning part of the developed predictive maintenance system. We modelled the task of estimating RUL of machines both as a classification and a regression problem.

- **Classification** approach provides a prediction of whether or not the machine will fail in next N hours.
- **Regression** approach provides a prediction of the remaining useful life of the machine (in hours/cycles).

The machine learning algorithm conists of a two-layered LSTM structure followed by a fully-connected layer to output the predictions. We also include dropout layers to minimize the overfitting caused by stacked LSTM layer. We use Adam optimizer for classification and RMSProp for regression; the models being trained until convergence.

---

## Dataset Used

The dataset used for training the models can be found <a href="https://ti.arc.nasa.gov/c/6/">here</a>. It includes sensor readings recorded during turbofan engine degradation simulation. The simulation was carried out using C-MAPSS. The dataset has four different sub-datasets simulated under different combinations of operational conditions and fault modes. Each sub-dataset has sensors readings of 100 different rotary machines from time corresponding to some nominal state upto the failure of the machine.

The figures below show a time series plot of sensor readings for two different engines - 

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/1.png" alt="Engine 3 - Dataset 1"/>
</p>

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/2.png" alt="Engine 8 - Dataset 2"/>
</p>

The figure below show histograms plot of sensor 4 readings for an engine when the engine operates normally (Label 0) and when the engines is about to fail (Label 1) - 

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/3.png" alt="Sensor - 4 Engine 3 - Dataset 1"/>
</p>

For regression problem, the sensor data is filtered using a Kalman filter to remove the noise present in the sensor readings. Finally, the dataset is normalized using Min-Max normalization. These preprocessing steps improve the performance of the machine learning system. 

Figure below show a time series plot of denoised sensor readings after employing these preprocessing steps - 

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/4.png" alt="After Kalman Filtering"/>
</p>

---

## Results

For training the models, we used a window of size 50 i.e, consecutive 50 sensor readings in each input sample. The results obtained for classification problem on test dataset 1 are tabulated below - 

| Model | Acc. | F1 |
|:--:|:--:|:--:|
| LSTM | 97 | 94 |
| LGBM | 92 | 82 |
| SVM | 93 | 84 |
| XGB | 92 | 82 |
{:class="table table-bordered"}

For regression task, the model achieved a mean squared error of around *309* while predicting RUL on the test data. 

##### Mean Squared Loss during Model Training

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/5.png" alt="MSE Model Training"/>
</p>

##### Plot of True and Predicted RUL Values

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/6.png" alt="True and Predicted RUL Values"/>
</p>

---

## Dashboard

The results are displayed on web dashboard and also notified to the operator using an Android app.

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/7.jpg" alt="Android App"/>
</p>

<p align="center">
<img src="{{ site.baseurl }}/assets/images/predmt/8.jpg" alt="Dashboard Web"/>
</p>


## Thank You!

---

## Important Links

<a href="https://github.com/imrahulr/Pred-Maintenance-Siemens">GitHub Repository</a><br>
<a href="https://www.hackerearth.com/challenges/hackathon/makeitreal/">Siemens MakeIT Real Hackathon</a><br>
<a href="https://ti.arc.nasa.gov/c/6/">Dataset Used</a><br>
<a href="https://en.wikipedia.org/wiki/Predictive_maintenance">Predictive Maintenance</a><br>
<a href="https://en.wikipedia.org/wiki/Kalman_filter">Kalman Filter</a><br>
<a href="https://github.com/imrahulr/Pred-Maintenance-Siemens/blob/master/dataset1/regression_auto_kalman_lstm.ipynb">Autoencoder + Kalman + LSTM for RUL prediction</a>
