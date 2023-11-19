---
title: "Self-driving cars: How can we empower an automobile the ability to interpret what it sees? (Part 1)"
date: 2021-12-28T11:15:14+01:00
categories:
  - portfolio
  - self-driving cars
tags:
  - deep learning
  - computer vision
  - artificial intelligence
  - autopilot
  - radar
  - lidar
  - camera
  - ultrasounds
  - pointcloud
  - autonomous cars
  - perception
  - lenet
  - polynomical fit
  - image preprocessing
  - canny edge detection
  - hough transformation
  - gaussian smoothing
  - region of interest
  - color selection
  - opencv
  - udacity
  - coursera
  - hsv
  - histograms
  - pipeline
  - classifier
  - traffic light
  - traffic sign
  - road lane
  - sensors
  - innovation
  - tesla
  - zoox
keywords:
  - tech
  - deep learning
  - computer vision
  - artificial intelligence
  - autopilot
  - radar
  - lidar
  - camera
  - ultrasounds
  - pointcloud
  - autonomous cars
  - perception
  - lenet
  - polynomical fit
  - image preprocessing
  - canny edge detection
  - hough transformation
  - gaussian smoothing
  - region of interest
  - color selection
  - opencv
  - udacity
  - coursera
  - hsv
  - histograms
  - pipeline
  - classifier
  - traffic light
  - traffic sign
  - road lane
  - sensors
  - innovation
  - tesla
  - zoox
comments: true
---

Self-driving cars are soon to become one of the most disruptive technologies of the last decades. They will have a deep impact on the economy, job market, transportation safety, transportation efficiency and our daily lives. In this series, I will show you how to learn from some practical use cases I applied through the Self-Driving Car Engineer Nanodegree at Udacity.

<!--more-->

>Given the fact that this technology evolves meteorically, some of the following techniques described in this series may no longer be used by the state-of-the-art applications like Tesla Autopilot. However, these foundations remain relevant.

# Introduction

We take for granted that living creatures can feel, taste, see, smell and hear. However, it is not trivial to replicate these on artificial creatures, _aka_ robots. The impulsive discoveries and innovations of the last decades in applied physics and electronics led to the ability of replicating the senses of living beings with devices known as sensors.

Particularly, a self-driving car is a mobile robot which should be able to see its environment. The rest of the senses are not fundamental for its correct operation. However, a human who drives, mainly uses its eyes to operate a car and sometimes its ears in case of emergencies like when another human honks.

What does also need an autonomous car besides to be able to see? Clearly, to be able to understand what it sees, which is not a trivial task either. This is known as the perception problem and there are many approaches that intend to solve it. At the end of the day, it is a computer behind the scenes making all the calculations and inferences to operate perception.

Traditionally, the discipline that approached this concern has been _Computer Vision_. In which the traditional methods consisted on _feature engineering_ and the use of _shallow classifiers_. However, another discipline has been arising at a fast pace because of its cutting-edge capabilities and variate applications, in which _Computer Vision_ is amongst of them. I am talking about the _Deep Learning_, a subset of _Machine Learning_ (which is also a subset of _Artificial Intelligence_), that has emerged mainly thanks to gamers.

<p></p>
{{< figure align="center" src="images/cv-vs-dl.jpg" title="Source: Deep Learning vs. Traditional Computer Vision paper (https://arxiv.org/abs/1910.13796)" >}}

Yes, gamers, these nerd guys (I have been one of them too) that spend countless hours playing in simulated 3D worlds. For these to be efficiently rendered, <abbr title="Graphic Processing Unit">GPUs</abbr> are required. The increasing influx of demand in the market has skyrocketed the development of more advanced <abbr title="Graphic Processing Unit">GPUs</abbr>, which can be advantageous for parallel computing required to make _Deep Learning_ training affordable in terms of time resources. The deeper a neural network gets, the more parameters (_weights_ and _biases_) must be computed (adjusted), so the longer it takes to complete the training. The use of <abbr title="Graphic Processing Unit">GPUs</abbr>, accelerates significantly the process of training and makes suitable the deployment of _deep learning_ applications to the market.

# Sensors

Traditionally, self-driving car prototypes have been using:

- **cameras**: useful for generating a sequence of images that can be fed to perception algorithms
- **LiDARs**: generate point clouds via rotating lasers that do not care about light conditions that can be fed to perception algorithms
- **radars**: useful for speed detection
- **ultrasounds**: useful for obstacle detection

There are advantages and disadvantages among these:
| Sensors     | Camera | LiDAR | Radar |
| ----------- | ------ | ----- | ----- |
| Resolution  | Good   | Ok    | Bad   |
| Noise       | Good   | Bad   | Bad   |
| Velocity    | Bad    | Bad   | Good  |
| All-weather | Bad    | Bad   | Good  |
| Size        | Good   | Bad   | Good  |

<p></p>
{{< figure align="center" src="images/waymo-sensors.jpg" >}}

{{< figure align="center" src="images/how-autonomous-car-sees-the-world.jpg" title="How autonomous cars see the world. Source: Intellias" >}}

However, there is an existing arising debate initiated by the <abbr title="Artificial Intelligence">AI</abbr> team at Tesla Inc.. They are the ones in charge of the development of the autonomous driving system Autopilot which is currently leading the technology ranking in the market of self-driving cars. Leaving technologically behind the competitors by years. They disagree on the use of LiDAR and, recently, on radar too [^1] for their AutoPilot autonomous navigation system.

>Lidar is a fool’s errand. Anyone relying on LIDAR is doomed. [They are] expensive sensors that are unnecessary. It’s like having a bunch of expensive appendices. Like, one appendix is bad, well now you have a bunch of them, it’s ridiculous, you’ll see.
>
>– Elon Musk, CEO Tesla Motors

That is why none of their cars have been released with a LiDAR sensor since the beginning as part of their master plan.

{{< figure align="center" src="images/tesla-sensors.jpg" >}}

Although, as stated before, they are dropping radar sensors which they relied on initially as part of a pivotal strategy because they think this sensor just adds noise to the system and is superfluous.

<p></p>
{{< figure align="center" src="images/tesla-vision-discarding-radar.jpg" title="Source: Tesla Support (transitioning tesla vision)" >}}

The fact that the market leaders in autonomous cars disagree with the establishment thinking is exciting, as it is a source of innovation to defy what is taken for granted.

Note that Tesla is the only company that has deployed a functional self-driving system at a global scale. This means that has generated a monopoly for data generation that is afterwards used to feed their deep neural networks. That is the reason why they can afford to drop such sensors and rely everything on their autopilot AI.

However, there are another companies that still agree on the use of radar and LiDAR, like Zoox, that are doing a great job innovating in the field.

<p></p>
{{< youtube BVRMh9NO9Cs >}}

# Self-driving car nanodegree projects

Theory is cool, but practice is more fun. In the following text, I will show you practical experience with actual code implementation with links to the source code. For this, I leveraged on the self-driving car engineer nanodegree at Udacity. There are more technical programs related to autonomous cars like the one provided by Coursera which is offered by University of Toronto. However, there is a significant disadvantage, which is that C++ is totally ignored from the syllabus and only Python programming language is contemplated.

In the robotics industry, C++ experience is a must, not optional. This is because under certain conditions, C++ can be hundreds of times faster [^2] than python. And, in many situations, it is not affordable to ignore C++ as a driven programming language for real-time systems. The program at Udacity offers Python and C++ know-how as part of their syllabus, that is why I chose it despite it is significantly more expensive.

The reviewed projects at Udacity related to the perception of an autonomous car are in the following fields of research:
- Computer Vision
- Deep Learning

## Computer Vision



### Traffic light classifier

In this [project](https://github.com/chbloca/udacity-self-driving-car-nanodegree/blob/master/traffic_light_classifier/Traffic_Light_Classifier.ipynb), the point was to build a classifier for images of traffic lights by implementing computer vision techniques which is tested afterwards with a dataset of images.

The pipeline implementation consisted of:
1. **Loading and visualizing the data**
2. **Pre-processing** by standardizing the input images and labels
3. **Feature extraction**, so that will help distinguish and eventually classify these images
4. **Classification and visualizing error** by writing a function fed with the image features which outputs a label
5. **Evaluate model** by analyzing the accuracy and iterate on improving the model

<br>

{{< figure align="center" width="50%" src="images/traffic-light-sample.jpg" title="Input data sample" >}}
{{< figure align="center" width="50%" src="images/traffic-light-standardization.png" title="Standardized data sample" >}}

For the scope of the project, the feature extraction was based on the image <abbr title="Hue Saturation Value">HSV</abbr> channels, rather than the <abbr title="Red Green Blue">RGB</abbr> channels. But, in order to improve the model, more feature extraction methods are encouraged such as the shape of the traffic lights.

<br>

{{< figure align="center" src="images/HSV.jpg" title="HSV channels" >}}
{{< figure align="center" src="images/traffic-light-classifier-channels.png" title="Standardized data sample and its HSV channels" >}}<br>
{{< figure align="center" src="images/traffic-light-classifier-histogram.png" title="HSV histograms" >}}

<br>

The classifier was good enough by filtering the images based on their hue ranges and saturation that was able to reach an accuracy of 98.7%. Now imagine adding features to the model and so on. 

For this case scenario, no machine learning approach was required. There is no need to use neural networks for everything, actually the rule of thumb is to avoid their use as their use increases the complexity of any system.

### Finding Lanes on the Road

#### Basic version

The point of this [project](https://github.com/chbloca/udacity-self-driving-car-nanodegree/blob/master/basic_lane_lines_detector/P1.ipynb) was to piece together a pipeline with the following methods to detect line segments in the image, and then average / extrapolate them, and draw them onto the image:
- color selection
- region of interest
- gray scaling
- Gaussian smoothing
- Canny Edge detection
- Hough Transformation

In computer vision, OpenCV is your best friend. It is the most extensive and recognized open source libraries related to computer vision.

This is the output result without extrapolation:
<video controls src="images/lane-detector-2.webm"></video>

This is the output result with extrapolation:
<video controls src="images/lane-detector-1.webm"></video>

#### Advanced version

The previous project had serious limitations concerning to road lane detection. To amend them, another [project](https://github.com/chbloca/udacity-self-driving-car-nanodegree/tree/master/advanced_lane_lines_detector) was done to enhance its capabilities.

The steps to proceed are:
1. Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
1. Apply a distortion correction to raw images.
1. Use color transforms, gradients, etc., to create a thresholded binary image.
1. Apply a perspective transform to rectify binary image ("birds-eye view").
1. Detect lane pixels and fit to find the lane boundary.
1. Determine the curvature of the lane and vehicle position with respect to center.
1. Warp the detected lane boundaries back onto the original image.
1. Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

{{< figure align="center" src="images/camera-calibration.jpg" title="Camera calibration" >}}
{{< figure align="center" src="images/camera-calibration-sample.jpg" title="Camera calibration sample" >}}
{{< figure align="center" src="images/corrected-to-warped.jpg" title="Image warping" >}}
{{< figure align="center" src="images/warped-to-thresholded.jpg" title="Image thresholded" >}}
{{< figure align="center" src="images/polynomial-fit.png" title="Polynomial fit" >}}

The result of this pipeline, unlike the basic version, allows adapting the detections to curved lanes and calculate the curvature of the rotation of the vehicle thanks to the polynomial fitting. Also, it is possible to infer the deviation of the center of the vehicle regarding the center of the lane.

<video controls src="images/advanced-lane-detector.webm"></video>

## Deep Learning

More info about deep learning-based image perception types [here]({{< ref "../../posts/master-thesis-post/index.md/#action" >}}), at the beginning of **Action** section.

### Traffic Sign Classifier

If we wanted to create a classifier for traffic signs with the traditional computer vision methods, we would require designing manually a huge amount of features to extract. This becomes more unfeasible the larger the output type set presents.

The solution to this is to let the algorithm create its own features instead of crafting them manually. In this kind of situations, deep learning is your best friend.

This [project](https://github.com/chbloca/udacity-self-driving-car-nanodegree/blob/master/traffic_sign_classifier/Traffic_Sign_Classifier.ipynb) in particular had the following pipeline structure:

1. Dataset Summary & Exploration
    1. Provide a Basic Summary of the Data Set Using Python, Numpy and/or Pandas
    1. Include an exploratory visualization of the dataset
1. Design and Test a Model Architecture
    1. Pre-process the Data Set (normalization, grayscale, etc.)
    1. Model Architecture
    1. Train, Validate and Test the Model
1. Test a Model on New Images
    1. Load and Output the Images
    1. Predict the Sign Type for Each Image
    1. Analyze Performance
    1. Output Top 5 Softmax Probabilities For Each Image Found on the Web
1. Visualize the Neural Network's State with Test Images

<br>

{{< figure align="center" src="images/traffic-sign-classifier.png" title="Traffic sign samples" >}}
{{< figure align="center" src="images/leNet.jpeg" title="LeNet-5 original architecture" >}}

<br>

  |      Layer      |                 Description                 |
  | :-------------: | :-----------------------------------------: |
  |      Input      |             32x32x1 Gray image              |
  | Convolution 5x5 | 1x1 stride, valid padding, outputs 28x28x6  |
  |      RELU       |                                             |
  |   Max pooling   |         2x2 stride, outputs 14x14x6         |
  | Convolution 5x5 | 1x1 stride, valid padding, outputs 10x10x16 |
  |      RELU       |                                             |
  |   Max pooling   |         2x2 stride, outputs 5x5x16          |
  |     Flatten     |                 outputs 400                 |
  | Fully connected |                 outputs 120                 |
  |      RELU       |                                             |
  | Fully connected |                 outputs 84                  |
  |      RELU       |                                             |
  | Fully connected |                 outputs 43                  |

Despite LeNet5 [^3] architecture was designed in the 90s, still provides very good results. For this project, it was achieved an accuracy of 94.5%.

# Conclusion

The perception problem is probably one of the most challenging regarding autonomous cars. In the real world, there is an unlimited level of uncertainty. And coding considering all the infinite factors, such as weird pedestrian behavior, dynamic weather and poor light conditions, is an unfeasible task if we do not leverage on statistics and probabilities.

Besides, the continuous rise of computer power allows implementing software strategies like deep learning that can deal with all these uncertainties. With the current track of records, at some point in the future, algorithms will be as good as humans in terms of perception. This means that automating transportation will become a reality.

[^1]: https://www.reuters.com/business/autos-transportation/tesla-drops-radar-is-autopilot-system-safe-2021-06-02/
[^2]: https://towardsdatascience.com/how-fast-is-c-compared-to-python-978f18f474c7
[^3]: https://www.datasciencecentral.com/profiles/blogs/lenet-5-a-classic-cnn-architecture