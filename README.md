# PID Controller
This project implements PID controller which uses Cross Track Error (CTE) to steer the vehicle around the track. 

## Input Data
- ["cte"] Cross track error based on cars position on the track (usually between -1.5, 1.5 if car is on the track).
- ["speed"] The car's speed in mph.
- ["steering_angle"] The car's stering angle in degrees (usually -25 to 25).

## Output Data
- ["steering_angle"] The car's desired stering angle between [-1, 1].
- ["throttle"] The car's desired throttle between [-1, 1].

## PID Controllers
As the name indicates, the controller is a linear combination of 3 components described below:
- `Proportional (P)`: This component is the current CTE of the car with a negative sign. This steers the car towards the center of the desired path to reduce CTE.
- `Derivative (D)`: This component is the rate of change of CTE with a negative sign. This counters the proprtional component to reduce overshoot or oscillation effect of the P component.
- `Integral (I)`: This component is the integral of CTE and counters systemic bias.

### Desired output: `-(Kp * p_error + Ki * i_error * i_error + Kd * d_error)`
- The coefficients for each component are tuned / optimized to get desired behavior.

## Solution
Two PID controllers are designed to control the steering and throttle of the car.
### PID Controller for steering angle
#### Starting values
- Since cte's range is between [-1.5, 1.5] and dsired steering angle is [-1, 1], Kp should be around or less than 0.67.
- Rate of change of CTE is an order of magnitude smaller. Kd needs needs to be about 10x
#### Tuning and observations
- Kp, Kd, Ki were manually iterated to find the optimum values. It was observed that Kd/Kp of 20x usually resulted in similar stable driving conditions. Steering was very sensitive for Ki and Ki=0 worked fairly well indicating very low systemic error.
```
`Final values (Kp, Ki, Kd): (0.2, 0., 4.)`
```
### PID Controller for throttle
- Target speed is set to 65 mph on straight roads and reduced during turns. The below calculation would result in a target speed of 30 mph during 25 degree steering turn.
  - `target_speed = 65. - 7. * sqrt(abs(angle))`
#### Tuning and observations
- Kp, Kd, Ki were manually iterated to find the optimum values.
```
`Final values (Kp, Ki, Kd): (0.15, 0., 0.1)`
```

## Video of PID in action
https://drive.google.com/file/d/16F-yKXEXPRqYxJH7qdPlYJWkq_6OYIqi/view

## Basic Build Instructions
1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./path_planning`.

## Dependencies
* cmake >= 3.5
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1
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
### Simulator.
You can download the Term2 Simulator which contains PID Controller (project 4) Project from the [releases tab (https://github.com/udacity/self-driving-car-sim/releases/tag/v1.45).
