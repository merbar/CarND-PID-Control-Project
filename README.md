# PID Controller
## Self-Driving Car Engineer Nanodegree Program

Controlling steer and throttle input via PID Controllers to drive a vehicle as fast as safely possible around a track in the Udacity Simulator.  

The vehicle has no knowledge about the world other than it's current deviation from center-lane (cross track error).  

This is part of a series of projects exploring different methods to autonomously drive a car around the given track. Others are:
* [MPC Control](https://github.com/merbar/CarND-MPC-Project)
* [Deep Learning](https://github.com/merbar/CarND-Behavioral-Cloning-P3)

## Result: [YouTube video: PID Control](https://youtu.be/IUm-smfyKUc)

## Implementation

The PID class contains the implementation of a PID Controller. Two PID Controllers are spawned to separately control steer and throttle.

## Parameter / Behavior Tuning

Parameters for throttle and steering PID Controllers where tweaked via trial and error.  

### Steering (PID)

My general strategy was to set the integral and derivative component to zero and find a value for `P` that would oscillate at a reasonable frequency.  

I then slowly increased `D` until the car stabilized. From here, I started tweaking as described below to minimize lap time.

Changes to the `P` and `D` components had the expected results
- Increasing `P` also increased the magnitude of the oscillation
- Increasing `D` smoothed the oscillation out. But if `D` was set too high, it destabilized the cars trajectory - especially through curves - by causing quick and extreme steering changes.
- Adding a little bit to the `I` component helped center the vehicle significantly through long, fast turns and was key to achieving speeds above 50 mp/h. While the car does not have a steering bias, the track mostly turns left. So with `I` at zero, the vehicle tends to stay to the right of center-lane and won't allow the throttle PID to accelerate significantly.

After a few runs, some relationships between the parameters stood out:
- If `P` is too low, the vehicle won't successfully navigate the two tight turns after the bridge.
- Oscillation could be smoothed out and the overall speed improved iteratively in two ways  
    - Increase `D`
    - If increasing `D` results in a less stable lap, then lower `P` and increase `I`

My default parameter settings still cause some oscillation at speeds above 60 mp/h. To enable the vehicle to continue accelerating, I am lowering all parameters significantly at high speed.

### Throttle (PD)

The throttle is inversely mapped to the cross track error - a high error means the speed should be reduced, while a low error means the car is stable and should accelerate.

I did not spend much time tweaking the values since I only needed the above described, fairly simple behavior.  

There are a few extra checks outside of the controller:
- Make sure the car does not stall on track by requiring a minimum speed
- Limiting the breaking power of the vehicle to not lose too much speed unnecessarily

---

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
* [uWebSockets](https://github.com/uWebSockets/uWebSockets) == 0.13, but the master branch will probably work just fine
  * Follow the instructions in the [uWebSockets README](https://github.com/uWebSockets/uWebSockets/blob/master/README.md) to get setup for your platform. You can download the zip of the appropriate version from the [releases page](https://github.com/uWebSockets/uWebSockets/releases). Here's a link to the [v0.13 zip](https://github.com/uWebSockets/uWebSockets/archive/v0.13.0.zip).
  * If you run OSX and have homebrew installed you can just run the ./install-mac.sh script to install this
* Simulator. You can download these from the [project intro page](https://github.com/udacity/CarND-PID-Control-Project/releases) in the classroom.

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./pid`. 
