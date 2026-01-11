--
## About Me

![alt text](portrait.png)

I'm currently a Research Engineer II at the Texas A&M University Engineering Experiment Station. I have a B.S. in Mechanical Engineering from the University of California, Irvine. I am aproximately halfway through my part-time aerospace engineering master’s degree at Purdue University, where I am specializing in autonomy and control systems. Previously, I was an Operations Engineer at a robotics startup called Stellar Pizza. Before that, I interned at Tesla, SpaceX, and Northrop Grumman, primarily working on test engineering.

My research interests lie in robot–environment interaction. As such, many of my projects involve utilizing my understanding of control systems, perception/sensing, localization, and path planning. As I dive deeper into my studies, I am also beginning to utilize optimization-based approaches like model predictive control and graph-based localization.

## Robotic Space Simulator KF/EKF/UKF
## Localization Of Spherical Robot Using ROS2 Package
## Custom Slope Detection Algorithm
<!--
Spherical robots show potential in descending slopes. The reason for this is that even uncontrolled descent of slopes will always result in the robot landing in the correct configuration to drive. In other words, a ball-shaped rover cannot be flipped upside down like a wheeled rover can.

Spherical robots often struggle with controlled descent of slopes. One of the many reasons for this is that the slope is often unobservable. To address this, we built a robotic system that attaches to the outside of the robot. This robotic sensor suite is actively driven so the 3D LiDAR sensor is always pointing at the floor.

Although this allowed for a good view of the slope angle, there was more that could be done with this time-of-flight sensor data. For example, the uncertainty was not quantified. This would mean that going down a concrete slope results in high slope certainty, while going down a rocky or gravel slope should indicate low certainty. The slope descent controller could then change its gains as a result of the slope’s uncertainty. Further, better measurements of uncertainty can be fed into global planners for improved localization accuracy.

One limitation of our system was the fact that the computer on the external sensor suite had low computational power. Sending every data point to the more powerful computer was difficult due to the low bandwidth capability between the two. As such, I decided against a particle filter and instead settled on a Gaussian mixture filter.

The derivation is shown below.

The key takeaways are this: the predict step assumes that the ground is a linear slope, and the update step refines this assumption based on the time-of-flight sensor data.

Because each of the 36 time-of-flight measurements is assumed to have independent Gaussian uncertainty, a simpler form of the Gaussian mixture filter can be used.
-->


## Robot Arm Spraying
## Modular Logging and Networking Systems
## Automatic Defrost Sequence
## Pressure Controller
## Machine Learning Pedestrian Navigation
