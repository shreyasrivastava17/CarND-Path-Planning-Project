﻿## Writeup Report

---

**CarND-Path-Planning-Project**

(Note - Hi , The visualization for the simulator on my machine is available at - https://youtu.be/Sej0m-YtBc0)

The goal of the project is to design a path planner that is able to create smooth, safe paths for the car to follow along a 3 lane highway with traffic.

## [Rubric](https://review.udacity.com/#!/rubrics/896/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Valid Trajectories:
 CRITERIA|RESULT|
|:--------------:|:------------:|
|The car is able to drive at least 4.32 miles without incident|I have driven the car for about five miles without an incident|
|The car drives according to the speed limit|The car does not exceed the speed limit of 50 miles per hour in the entire run of 5 miles.|
|Max Acceleration and Jerk are not Exceeded|There was no warning for the max acceleration or the jerk being exceeded the max specified limit in the simulator. |
|Car does not have collisions| There is no collision message coming up in the simulator of collision in the entire run|
|The car stays in its lane, except for the time between changing lanes|Yes the car stays in the lanes if there is no car ahead of it and it does not have to change lanes.|
|The car is able to change lanes|The car is able to change lanes if there is a slow car ahead of it and there is no car in either left of the right lanes in the buffer gap of 40 m ahead and behind of the car.|

---
#### Model description

Simulator sends data in JSON format and is listened in main.cpp. Data contains Car's coordinate, speed, velocity, yaw rate and Frenet coordinates. Simulator also returns data from sensors which contains data of other cars.

Our model for Path Planning can be broken down in three modules

-   **Prediction**  Sensor fusion and simulator data is used to predict the behavior of the other moving objects such as cars in the simulator.
    
-   **Behavior Planning**  This module of the path planner deals with what action we need to take according to the data that we receive from the prediction module. There are majorly three behaviors that our car can perform i.e. be in the lane it is in or change lane to the left or change lane to the right. All these behaviors maintain the desired speed limit, acceleration and jerk by accelerating or decelerating when ever required as per the situation. I have taken up a distance of 30 m as the gap between our car any other vehicle ahead of us in our lane to start decelerating and see if there is a possibility of lane change. I have taken a buffer gap of 40 m both ahead and behind our car to change the lane. 
    
-   **Trajectory Generation**  Our car generates the trajectory based on the speed, position, lane, car coordinates and past path points. To make trajectory smooth we use points that are remaining from the previous path sent to the simulator that were not used up by the car. If there are no previous points then we calculate the previous points from the current yaw and current car coordinates. I have used the spline library to generate the trajectory. The car always has set of 50 points ahead of it.
Below is the code that shows the trajectory generation portion and the use of spline
```
//Getting the waypoints to fit the spline
vector<double> next_wp1 = getXY(car_s+30,(2+4*lane),map_waypoints_s,map_waypoints_x,map_waypoints_y);
vector<double> next_wp2 = getXY(car_s+60,(2+4*lane),map_waypoints_s,map_waypoints_x,map_waypoints_y);
vector<double> next_wp3 = getXY(car_s+90,(2+4*lane),map_waypoints_s,map_waypoints_x,map_waypoints_y);

//Setting the points on spline
tk::spline s;
s.set_points(ptsx,ptsy);

``` 
