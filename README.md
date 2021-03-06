# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---
## Introduction
The goal of this project is to develop a Model Predictive Controller to drive a car on the track in simulator. The controller communicates with simulator via websocket by receiving the input from the simulator, calculating and sending steering and acceleration command back to the simulator.

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)
* [uWebSockets](https://github.com/uWebSockets/uWebSockets)
  * Run either `install-mac.sh` or `install-ubuntu.sh`.
  * If you install from source, checkout to commit `e94b6e1`, i.e.
    ```
    git clone https://github.com/uWebSockets/uWebSockets
    cd uWebSockets
    git checkout e94b6e1
    ```
    Some function signatures have changed in v0.14.x. See [this PR](https://github.com/udacity/CarND-MPC-Project/pull/3) for more details.

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* [Eigen](http://eigen.tuxfamily.org/index.php?title=Main_Page). This is already part of the repo so you shouldn't have to worry about it.
* Simulator. You can download these from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* Not a dependency but read the [DATA.md](./DATA.md) for a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./mpc`.

## Tips

1. It's recommended to test the MPC on basic examples to see if your implementation behaves as desired. One possible example
is the vehicle starting offset of a straight line (reference). If the MPC implementation is correct, after some number of timesteps
(not too many) it should find and track the reference line.
2. The `lake_track_waypoints.csv` file has the waypoints of the lake track. You could use this to fit polynomials and points and see of how well your model tracks curve. NOTE: This file might be not completely in sync with the simulator so your solution should NOT depend on it.
3. For visualization this C++ [matplotlib wrapper](https://github.com/lava/matplotlib-cpp) could be helpful.)
4.  Tips for setting up your environment are available [here](https://classroom.udacity.com/nanodegrees/nd013/parts/40f38239-66b6-46ec-ae68-03afd8a601c8/modules/0949fca6-b379-42af-a919-ee50aa304e6a/lessons/f758c44c-5e40-4e01-93b5-1a82aa4e044f/concepts/23d376c7-0195-4276-bdf0-e02f1f3c665d)
5. **VM Latency:** Some students have reported differences in behavior using VM's ostensibly a result of latency.  Please let us know if issues arise as a result of a VM environment.

## Editor Settings

We've purposefully kept editor configuration files out of this repo in order to
keep it as simple and environment agnostic as possible. However, we recommend
using the following settings:

* indent using spaces
* set tab width to 2 spaces (keeps the matrices in source code aligned)

## Code Style

Please (do your best to) stick to [Google's C++ style guide](https://google.github.io/styleguide/cppguide.html).

## Project Instructions and Rubric
---
### 1. The Model: 
**Student describes their model in detail. This includes the state, actuators and update equations**

There are two parts in designing the Model Predictive Control. First part is the setup and the second part is the process of the controller in the real time application.

##### Setup the Controller: ( Picture 1)
<img src="images/image2.png" width="500px"/>
Setting up controller by :

* Defining the duration of trajectory  by defining N and dt
* Define the vehicle MPC and the limitation of actuation (Constrains).
* Setup the cost function

##### Processing the Controller (Picture 2)
<img src="images/image3.png" width="500px"/><br>
Start by passing the current state into the Solver. The Solver use Model Predictive Control, Constrains, and Cost function to produce vector imput that use to control the actuation that are acceleration and steering angle.

### 2. Timestep Length and Elapsed Duration (N & dt) : 
**Student discusses the reasoning behind the chosen N (timestep length) and dt (elapsed duration between timesteps) values. Additionally the student details the previous values tried.**

The values chosen for N and dt are 10 and 0.1, respectively. The value is suggested by the discussion in udacity group. These values mean that the optimizer is considering a one-second duration in which to determine a corrective trajectory. Adjusting either N or dt will produce erratic behavior. Other values tried include 15 / 0.2, 8 / 0.05, and 6 / 0.15, and some other values.

### 3. Polynomial Fitting and MPC Preprocessing: 
**A polynomial is fitted to waypoints. If the student preprocesses waypoints, the vehicle state, and/or actuators prior to the MPC procedure it is described.**

The waypoints are preprocessed by transforming them to the vehicle's perspective (see main.cpp lines 108-113). This simplifies the process to fit a polynomial to the waypoints because the vehicle's x and y coordinates are now at the origin (0, 0) and the orientation angle is also zero.

### 4. Model Predictive Control with Latency: 
**The student implements Model Predictive Control that handles a 100 millisecond latency. Student provides details on how they deal with latency.**

The approach to dealing with latency was twofold: the original kinematic equations depend upon the actuations from the previous timestep, but with a delay of 100ms (which happens to be the timestep interval) the actuations are applied another timestep later, so the equations have been altered to account for this (MPC.cpp lines 104-107). Also, in addition to the cost functions suggested in the lessons (punishing CTE, epsi, difference between velocity and a reference velocity, delta, acceleration, change in delta, and change in acceleration) an additional cost penalizing the combination of velocity and delta (MPC.cpp line 63) was included and results in much more controlled cornering.



