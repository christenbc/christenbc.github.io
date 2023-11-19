---
title: "How inefficient can be mapping for robot navigation in the industry?"
date: 2021-12-04T13:47:40+01:00
categories:
- portfolio
- robotics
tags:
- mir100
- industrial
- robots
- fleet
- factory
- layout
- inefficiency
- lidar
- stereo camera
- path planning
- intel real-sense
- software
- ros
- mapping
keywords:
- mir100
- industrial
- robots
- fleet
- factory
- layout
- inefficiency
- lidar
- stereo camera
- path planning
- intel real-sense
- software
- ros
- mapping
comments: true
---

Traditional mapping for mobile robots in a complex and dynamic environment can be deceiving for these animals. Time and money are likely to be wasted in the long run. An alternative is proposed prototyping with a MiR100.
<!--more-->

# Introduction

Industries use mobile robots mainly for automating repetitive and boring tasks related to the logistics of resources transportation and manipulation. Usually, the purpose of this is to reallocate workforce labor to more fruitful tasks.

Any mobile robot that has the ability of moving around on its own as an intelligent agent [^1] requires of a map of its environment. This is part of what is known as robot perception, which is the way to provide context and knowledge of the state to the agent about its environment.

That map is basically a grid of cells which tells the possible following states:
- occupied: part the obstacle is that coordinate 
- freed: there is nothing in that coordinate
- uncertain: there is no information about such coordinate

and can be used not only by one mobile robot, but also for a fleet of mobile robots.

# Situation

Many industrial robots commercially available come with standard mapping techniques. These consist of gathering all the data from the LiDAR [^2] sensors.  

{{< youtube _uV6zP95xM0 >}}

<br>

The following video basics are the same but the LiDAR readings are represented real-time. Notice how such readings are mapped as occupied cells in the 2-D grid called map.  

{{< youtube jgPuUOX1Pho >}}

<br>

Imagine that the executive board of the company you are working for approved a strategic plan based on the Industry 4.0 principles.
This plan consists of deploying a fleet of robots that will automate the materials' transportation within the facilities.
Your boss now asks you to obtain a map of the layout that will be used for that fleet of robots to operate autonomous navigation.
As a good employee, you accept the task and start mapping around the facility with a mobile robot like the _MiR100_ in the video.

But the reality is that many factories are cluttered with stuff and personnel moving around.  

{{< figure align="center" src="images/cluttered-factory.png" title="A factory cluttered with workers and moving elements">}}

So while you are mapping, once you encounter any moving obstacle (_dynamic object_ from now on) your map is messed up with data which is false. These dynamic objects will not be there afterwards. In the end, after a task that might have taken you in some cases almost an hour, you ignore these noise added to your map and save the map for the production.

Because of your job, that fleet of robots is now ready to be deployed and start performing their work. Everything looks fine. Nice work!

However, there is a little evil behind everything. Well hidden but dragging out the overall performance of the fleet. Why?
Imagine a simple case scenario. While you were mapping a layout you encountered a worked doing his labors and he was registered on the map as an obstacle. You ignore that fact and once you end the map, you feed it to an autonomous robot so that it can operate.

Now the worker has gone for lunch, and the robot is operative. It is commanded to transport a good from a start position of the facility a goal position. This is done by the global path planner, which is the intelligence behind that makes the robot autonomous. While performing the calculations for the path planning, the robot still thinks that the worker is still there because is fed with a map that is outdated.

>On the other hand, the local path planner is the one in charge of improvising along the way if the robot encounters an actual obstacle so that can maneuver properly

{{< figure align="center" width="50%" src="images/robot-moving-with-obstacle.jpg" title="Map with dynamic object" >}}

{{< figure align="center" width="50%" src="images/robot-moving-without-obstacle.jpg" title="Map without dynamic object" >}}

This means that, in certain circumstances like the one depicted in the pictures above, the path might become longer with dynamic objects.

In order words, it will take longer for the robot to reach the goal. Which means, it will perform fewer tasks under a period of time.
Imagine this effect compounded with multiple dynamic objects on the path, return trips and fleet of robots. 

> In terms of business, a waste of time, energy and, ultimately, money. An inefficiency that must be addressed.

# Task

How is possible to discard a dynamic object from a map that is already deployed? With the current standard techniques is not possible. So an approach to overcome this issue is to renew the map with an updated version. However, this implies the operator to remap everything again. Which again, will face the same problems as before.

> A more seamless approach is to address the problem from the root: at the time of mapping.

In my case, I had access to a [MiR100](https://www.mobile-industrial-robots.com/solutions/robots/mir100/), an autonomous mobile robot with odometry, IMU, LiDARs, ultrasound sensors, and a stereo camera. With this equipment, I had to figure out how to overcome this problem as a Research & Development project solution.  
<br>

{{< figure align="center" src="images/mir-sensors.png" title="MiR100 stereo camera and LiDAR" >}}

Not only that, but also automate the mapping procedure. Do you remember previously I mentioned that an operator had to control the robot to move it around in order to generate the map? There are _SLAM_[^3] techniques oriented to map exploration capable of replacing human supervision and operation.  
<br>

{{< figure align="center" src="images/mir-perception.png" title="A sample of the perception of the robot by using RViz" >}}

Best approach to innovate is to wonder the proper questions and discard what is taken for granted:

> Why just LiDARs to generate a map?  
> Why not leverage additional sensors to produce a map?  
> How can the robot notice what objects are _dynamic_ and what are static?

# Action

A person or an animal uses its eyes to distinguish among stuff, right?  
Why not trying to replicate the millions of years of evolution in biological beings?  
<br>
| Biological being | Robotic entity |
| --- | --- |
| Eyes | Stereo camera |
| Part of the brain in charge of vision processing | Computer Vision and Deep Learning algorithms |

Let's then leverage on the tools and techniques provided by the Machine Learning and Computer Vision fields. So that, it is possible to build a system that allows the robot interpret autonomously every object it sees while mapping the area with the LiDARs.

Currently, the available methodologies are:  
<br>
| Image Perception techniques | Purpose |
| --- | --- |
| Image Classification | Identify what is observed in the image |
| Object Detection | Identify what is observed in the image and where (wrapped within a bounding box) |
| Semantic Segmentation | Identify what is observed in the image and where (masked all the pixels belonging to that overlapping section)  |

For simplicity, I decided to go for Object Detection for the following reasons:
- the robot needs to know also the position of the detections (it will be explained the reason later)
- as most of the available open source packages were more mature and stable than the ones as regard for Semantic Segmentation at the moment of the implementation

Particularly, [YOLOv3](https://arxiv.org/abs/1804.02767) was the one selected as it broke the benchmarks top of the competence.
In object detection, benchmarking is determined by the correlation between mAP [^4] (precision) and inference time. When implementing an object detector, you can configure its operation parameters in terms of requested precision level and speed. The configuration of these suppose a trade-off:
- if you prefer a faster model, then you will sacrifice precision
- if you prefer a more precise model, then you will sacrifice speed

{{< figure align="center" src="images/yolo-benchmarks.png" title="YOLOv3 vs competence" >}}

{{< figure align="center" src="images/yolo-detections.png" title="YOLOv3 output screenshot" >}}

{{< youtube MPU2HistivI >}}
<br>
Any deep learning model must be trained with a dataset. Fortunately, YOLOv3 comes pre-trained with the COCO [^9] dataset. Which is an open-source project of hundreds of thousands of labeled pictures that can be used to feed neural networks trainings.  

Mapping an environment is a real-time operation in which computing time is a very important factor to consider. Let's remember that now the approach is to combine the LiDARs and the detections of YOLOv3 as an integrated system.  

{{< figure align="center" src="https://cocodataset.org/images/coco-examples.jpg" title="Coco dataset examples" >}}

{{< figure align="center" width="50%" src="images/robot-detection-camera-lidar-with-obstacle.png" title="Environment with dynamic object" >}}
{{< figure align="center" width="50%" src="images/robot-detection-camera-lidar-without-obstacle.png" title="Environment without dynamic object" >}}  

<br>

The point of the proposed [^5] approach is to ignore the dynamic detections during the map generation as these will not be there later.

{{< figure align="center" src="images/sensor-fusion-diagram.png" title="Laser Filtering Module diagram extract from the thesis" >}}

The pseudocode of the concept is the following:

```
S ⟵ getLaserScanPoints();
B ⟵ getBoundingBoxes();
StructScan ⟵ ∅;
NonStructScan ⟵ ∅;
PClass ⟵ StructPoint;

for each Sn in S do
    Pn ⟵ convertToCartesionPoint(Sn);
    for each Bn in B do
        if isInsideBoundingBox(Pn, Bn) then
            PClass ⟵ NonStructPoint;
            break;
        end if
    end for
    if PClass == StructPoint then
        StructScan += Sn;
    else NonStructScan += Sn;
    end if
end for;
```

In other words, the LiDAR data output is a set of points (with this data [structure](http://docs.ros.org/en/noetic/api/sensor_msgs/html/msg/LaserScan.html)) that are discarded if overlapped with a bounding box of a detection or accounted otherwise.

Let's remember, in addition to this, the mapping needs to be performed autonomously for this project. I am not going to explain any details about since it is out of the scope of this post. Only it is worth to mention it was used strategy based on RTT [^6]exploration.

>All the software is integrated in ROS (Robotic Operating System). This middleware handles all the internal communications among sensors, drivers, actuators and computers.

At the end, the system integration design is constrained by the fact it is not possible to modify any software inside the MiR100 as it is encrypted by the manufacturer. The way to by-pass this issue is to run everything from an external computer. 

In my case, my available resource was a laptop with Ubuntu as OS [^7]. The problem was that it had not enough graphical computing capabilities to execute real-time the RRT autonomous exploration in cooperation with YOLOv3. Therefore, I had to outsource this task to an external GPU [^8].

{{< figure align="center" src="images/components-integration-diagram.png" title="Implementation components diagram" >}}

# Results

The results are promising. With this approach, the implementation avoids the laser scans from the LiDAR belonging to the detection areas to be considered as obstacles in the map.
<br>

{{< figure align="center" src="images/results-implementation-person.png" title="Screenshot of the execution. Red dots are the laser scans discarded during the mapping while the green ones are labeled as obstacles" >}}
<br>
{{< youtube rZZBv2fYgHc >}}
<br>
{{< figure align="center" src="images/results-implementation-chairs.png" title="Test scenario with chairs. Notice that in the filtered map, black dots (transversal cut of pneumatic cylinder chair) are gone" >}}

Of course, the strategy has room for future work and improvement. But the purpose of the implementation is to prove that with this strategy, the generated maps are free from dynamic objects.

By this way, now fleet of mobile robots can now generate path planning without considering objects that are supposed to not be there later.

>With this approach, we ensure robot fleet operations are more efficient in terms of energy and time. At the end, profits return by decreasing costs associated to inefficiencies 

All the information is in my Master's thesis [publication](https://trepo.tuni.fi/bitstream/handle/123456789/27700/blom-dahlcasanova.pdf?sequence=4&isAllowed=y), feel free to take a look if you want to dig in the topic.

[^1]: a computer program that performs various actions continuously and autonomously on behalf of an individual or an organization
[^2]: Light Detection and Ranging or Laser Imaging Detection and Ranging
[^3]: Simultaneous Location and Mapping
[^4]: [mean Average Precision](https://jonathan-hui.medium.com/map-mean-average-precision-for-object-detection-45c121a31173)
[^5]: [Autonomous Robot Exploration With Selective Object Discrimination by Using Deep Learning Object Detection](https://trepo.tuni.fi/bitstream/handle/123456789/27700/blom-dahlcasanova.pdf?sequence=4&isAllowed=y)
[^6]: [Rapidly-exploring Random Tree](http://wiki.ros.org/rrt_exploration)
[^7]: Linux-based Operating System
[^8]: Graphic Processing Unit
[^9]: [Common Objects in Context](https://cocodataset.org/#home)

