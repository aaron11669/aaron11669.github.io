<style>
/* Minima-style expandable sections */
details.project > summary {
  list-style: none;
  cursor: pointer;

  font-size: 1.35em;
  font-weight: 500;
  line-height: 1.2;

  margin: 1.4em 0 0.6em 0;
  padding-bottom: 0.25em;
  border-bottom: 1px solid #e8e8e8;

  color: #111;
}

details.project > summary::-webkit-details-marker {
  display: none;
}

details.project > summary::before {
  content: "▸";
  display: inline-block;
  margin-right: 0.5em;
  color: #828282;
}

details.project[open] > summary::before {
  content: "▾";
}

details.project[open] {
  margin-bottom: 1.2em;
}
</style>

--
# About Me

<img src="portrait.png" width="60%">

I'm currently a Research Engineer II at the Texas A&M University Engineering Experiment Station working in the Robotics and Automation Design Lab. I have a B.S. in Mechanical Engineering from the University of California, Irvine. I am aproximately halfway through my part-time aerospace engineering master’s degree at Purdue University, where I am specializing in autonomy and control systems. Previously, I was an Operations Engineer at a robotics startup called Stellar Pizza. Before that, I interned at Tesla, SpaceX, and Northrop Grumman, primarily working on test engineering.

My professional interests lie in robot–environment interaction. As such, many of my projects involve utilizing my understanding of control systems, perception/sensing, localization, and path planning. As I dive deeper into my studies, I am also beginning to utilize optimization-based approaches like model predictive control and graph-based localization.

# Projects

<details class="project" markdown="1">
  <summary>Rover Leg Optimal Impedance Controler Design (WIP)</summary>
</details>

<details class="project" markdown="1">
  <summary>Robotic Space Simulator KF/EKF/UKF (WIP) </summary>
</details>

  
<details class="project" markdown="1">
  <summary>Localization Of Spherical Robot Using EKF and Factor Graph SLAM </summary>

## Local Estimation Using EKF:

### Problem Statement

The robot did not know where it was. We had to demonstrate low level autonomy for a military customer in a little over a month.

### What I did

For quick (around 40Hz) localization of the robot, I used a standard robotic localization package called "ROS2 Robot localization" (https://github.com/cra-ros-pkg/robot_localization). This utilized an EKF and allowed for quick relatively continuous localization for rapid control and pathing decisions using the existing sensor suite.

Because the platform is a non-traditional rover, the standard configuration required significant customization. This included transforming IMU and magnetometer measurements into the Earth frame and incorporating platform-specific dynamics into the estimation pipeline to maintain filter consistency at high update rates.

### Notable Achevements
+ Rapid integration and tuning enabled the team to meet aggressive demonstration deadlines
+ Extended EKF cross-covariance logic to enable water detection as a by-product of state estimation
+ Won a paid research contract with the military customer

### Media

<details class="code" markdown="1">
  <summary><strong>View code snippet</strong></summary>

~~~cpp
// Convert magnetometer measurements into a yaw-only orientation estimate.
// Roll and pitch are intentionally omitted to avoid magnetometer corruption
// and are handled by a separate IMU function.

void KinematicTransformer::magnetometer_callback(
    const sensor_msgs::msg::MagneticField::SharedPtr msg)
{
    sensor_msgs::msg::Imu imu_msg;
    imu_msg.header.frame_id = "robot";
    imu_msg.header.stamp = this->get_clock()->now();

    // Project magnetic field onto horizontal plane
    double mx = msg->magnetic_field.x;
    double my = msg->magnetic_field.y;
    double mz = msg->magnetic_field.z;

    double norm_inv = 1.0 / std::sqrt(mx * mx + my * my);
    mx *= norm_inv;
    my *= norm_inv;
    mz *= norm_inv;

    tf2::Vector3 mag_sensor(mx, my, mz);

    // Rotate into earth frame using current body orientation
    tf2::Vector3 mag_earth = tf2::quatRotate(
        tf2_quat.inverse(), mag_sensor); //tf2_quat is an output of imu callback and contains the IMU rotation

    // Compute yaw from horizontal magnetic field
    double yaw = std::atan2(mag_earth.y(), mag_earth.x());

    tf2::Quaternion q;
    q.setRotation(tf2::Vector3(0, 0, 1), yaw);

    imu_msg.orientation.x = q.x();
    imu_msg.orientation.y = q.y();
    imu_msg.orientation.z = q.z();
    imu_msg.orientation.w = q.w();

    // Covariance explicitly provided for downstream sensor fusion
    imu_msg.orientation_covariance = {
        mag_orientation_cov[0], 0.0, 0.0,
        0.0, mag_orientation_cov[1], 0.0,
        0.0, 0.0, mag_orientation_cov[2]
    };

~~~

</details>

*Click above to see code snippet detailing IMU transformation code.*


<video width="80%" controls>
  <source src="path_planning_on_the_beach_.mp4" type="video/mp4">
</video>

*Video of path planning on the beach using simple l1 planner enabled by EKF/Robot localization package.*

<video width="60%" controls>
  <source src="foxglove_localization_visualizer.mp4" type="video/mp4">
</video>

*Foxglove Visualization of Robot Localizing in high bay (URDF Missing/low res = needs to be updated)*


## Global Estimation Using Factor Graph SLAM:

</details>



<details class="project" markdown="1">
  <summary>Custom Slope Detection Algorithm (WIP) </summary>

<!--
I worked on slope descent for a spherical robot, focusing on estimating both slope angle and uncertainty. To improve observability, I designed an external, actively actuated time of flight sensor module that continuously points toward the ground.

Due to strict compute and bandwidth constraints, I implemented a Gaussian mixture filter to estimate slope geometry and confidence directly on the sensor module. This allowed the descent controller to adapt its gains based on terrain quality and provided higher-quality inputs to global planners.

Notable Achevements:
+ Implemented real-time Gaussian mixture filtering with aggressive component trimming
+ Derived sensor uncertainty models based on time-of-flight sensor geometry

*Add a powerpoint plot*
*Add the predict and the update steps*
*Add photo of the remora*
*EVENTUALLY add a video of it working*

-->
</details>

<details class="project" markdown="1">
  <summary>Robot Arm Spraying</summary>

### Problem Statement

Spherical robot shells are manufactured using a spray process. Because this process is typically performed by hand, a lack of shell consistency can lead to control issues, including reduced control authority and increased sensitivity to the drive system. It also resulted in inconsistent control authority and sensitivity between robots.

### What I did

To improve this process, I replaced manual spraying with a robotic arm–based spraying system. This significantly improved shell consistency, which in turn improved control performance and allowed for the use of a less aggressive controller.

### Notable Achevements:
+ Automated both the spinner and the robot arm using ROS2/C++
+ Derived kinematic equations to achieve different shell thicknesses as a function of spray speed
+ Implemented closed-loop thickness correction using a 3D surface map of the sphere

### Media

<video width="80%" controls>
  <source src="arm_spray.mp4" type="video/mp4">
</video>

*Video of the robot arm spraying*

<p>
  <img src="latitude_plots.png" width="40%" style="margin-right:12%;" />
  <img src="longitude_plots.png" width="40%" />
</p>

*Shell thicknesses along the latitude and longitude of the shell. A much more consistant thickness can be observed in the robot arm sprayed shells.*

<img src="sprayer_mount.png" width="40%">

*Robotic arm–mounted spray system.*
</details>


<details class="project" markdown="1">
  <summary>Pressure Controller</summary>

### Problem Statement

A general pressure control system was needed in the lab. Here were some of the projects that requested such a thing:

+ Inflate and deflate shperical inflatable rover (shown in my other projects)
+ Blow mars and lunar regolith simulant into custom motors for extended period of time
+ Cycle testing of robot that uses inflatable baloon

### What I did

I designed and implemented a general-purpose pressure control system for laboratory use. The system includes a custom PCB and embedded software, and interfaces directly with the 40-pin GPIO header of an NVIDIA Jetson or Raspberry Pi. Once configured, the controller uses a custom ROS2 Interface to controll a solenoid valve or a compressor to regulate pressure or track a changing setpoint.

### Notable Achievements

+ Designed, documented, and trained lab members on a ROS 2–based pressure controller toolbox
+ Developed a shield board using a shared GPIO pinout to ensure cross-compatibility between Raspberry Pi and NVIDIA Jetson platforms
+ Designed and manufactured the PCB, and implemented the full embedded control software stack

### Media

<video width="30%" controls>
  <source src="pressure.mp4" type="video/mp4">
</video>

*Video of Custom PCB Implementation working via Steam Deck*

<img src="PCB_schematic.JPEG" width="100%">

*PCB Schematic*

<p>
  <img src="PCB_layout.png" height="300" style="margin-right:12%;" />
  <img src="PCB_manufactured.png" height="300" />
</p>

*PCB Layout and Manufactured PCB on Jetson Orin Nano*
</details>
