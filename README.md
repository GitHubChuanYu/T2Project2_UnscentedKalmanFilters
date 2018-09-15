# Chuan's Unscented Kalman Filter Project
Self-Driving Car Engineer Nanodegree Program

In this project I will utilize an Unscented Kalman Filter to estimate the state of a moving object of interest with noisy lidar and radar measurements. Passing the project requires obtaining RMSE values that are lower that the tolerance outlined in the project rubric. 

This project involves the Term 2 Simulator which can be downloaded [here](https://github.com/udacity/self-driving-car-sim/releases)

This repository includes two files that can be used to set up and intall [uWebSocketIO](https://github.com/uWebSockets/uWebSockets) for either Linux or Mac systems. For windows you can use either Docker, VMware, or even [Windows 10 Bash on Ubuntu](https://www.howtogeek.com/249966/how-to-install-and-use-the-linux-bash-shell-on-windows-10/) to install uWebSocketIO. Please see [this concept in the classroom](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/16cf4a78-4fc7-49e1-8621-3450ca938b77) for the required version and installation scripts.

Once the install for uWebSocketIO is complete, the main program can be built and ran by doing the following from the project top directory.

1. mkdir build
2. cd build
3. cmake ..
4. make
5. ./UnscentedKF

[//]: # (Image References)
[image1]: ./UnderestimateLidarNIS.png
[image2]: ./UnderestimateRadarNIS.png
[image3]: ./GoodestimateLidarNIS.png
[image4]: ./GoodestimateRadarNIS.png

## Other Important Dependencies
* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./UnscentedKF` Previous versions use i/o from text files.  The current state uses i/o
from the simulator.

## Own project work based on original project respository

### Unscented Kalan filter code update
The main code changes are:
* ukf.cpp:
  * Initializing parameters and variables for unscent kalman filter
  * Define ProcessMeasurement; Prediction; UpdateRadar; UpdateLidar functions
* tools.cpp:
  * Update CalculateRMSE function
* ukf.h:
  * Add variables for NIS_L and NIS_R for storing NIS values for Lidar and Radar respectively
  
### Tuning the process noise parameters std_a_ and std_yawdd_
* First I analyze the sensor measurement data and get the maximum acceleration and yaw acceleration is about 0.1m/s^2 and 0.14 rad/s^2, so I tried std_a_ = 0.05 and std_yadd_ = 0.07. It turns out the it is a little bit underestimate with NIS like this for Lidar and Radar respectively:

![alt text][image1]

![alt text][image2]

And the RMSE is not good with very high values:

| Item        		|     RMSE	        					| 
|:---------------------:|:---------------------------------------------:| 
| px         		| 0.18   							| 
| py     	| 0.17	|
| vx	    | 0.47 |
| vy	| 0.38 |

* Then I bumped up the parameters to std_a_ = 0.5 and std_yadd_ = 0.7. I am getting both good RMSE and NIS:

![alt text][image3]

![alt text][image4]

And the RMSE is not good with very high values:

| Item        		|     RMSE	        					| 
|:---------------------:|:---------------------------------------------:| 
| px         		| 0.06   							| 
| py     	| 0.084	|
| vx	    | 0.32 |
| vy	| 0.23 |

### Test UKF with Lidar and Radar only sensor measurements

The compared results among UKF with Lidar and Radar only sensor measurements and sensor fusion UKF are:

| Item        		|     Lidar Only UKF RMSE	    | Radar Only UKF RMSE  | Sensor Fusion UKF RMSE |
|:---------------------:|:-------------------:|:-------------------:|:-------------------:|  
| px       | 0.09   		| 0.14   		| 0.06   | 
| py     	| 0.092	| 0.20	| 0.084	|
| vx	    | 0.48 | 0.38	| 0.32	|
| vy	| 0.234 | 0.232	| 0.23	|

It is evident from above comparing that Lidar sensor is good at providing accuracy date of oject position of px and py while Radar sensor helps a lot to provide object velocity vx and vy. With sensor fusion UKF, it combines the advantages of both Lidar and Radar sensors to estimate the position and velocity of object.

### Compare UKF with EKF

| Item        		|     UKF RMSE	    | EKF RMSE  | 
|:---------------------:|:-------------------:|:-------------------:|
| px       | 0.06  		| 0.97   		|
| py     	| 0.084	| 0.085	|
| vx	    | 0.32 | 0.45	|
| vy	| 0.23 | 0.44	|

As we can see from above comparing result, UKF is better than EKF on sensor fusion results, especially in estimating vx and vy.
