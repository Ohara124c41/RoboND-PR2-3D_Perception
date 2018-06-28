## Project: 3D Perception Pick & Place (Amazon Robotics Challenge) http://robohub.org/amazon-picking-challenge/

___

#### Researcher: Christopher Ohara
#### Program: Udacity Robotics Nanodegree
___
[//]: # (Image References)

[image1]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/001.png?raw=true
[image2]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/002.png?raw=true
[image3]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/003.png?raw=true
[image4]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/005.png?raw=true
[image5]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/201.png?raw=true
[image6]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/301.png?raw=true
[image7]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/302.png?raw=true
[image8]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/303.png?raw=true
[image9]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/304.png?raw=true
[image10]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/images/305.png?raw=true
[image11]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/001.png?raw=true
[image12]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/002.png?raw=true
[image13]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/003.png?raw=true
[image14]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/004.png?raw=true
[image15]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/005.png?raw=true
[image16]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/100.png?raw=true
[image17]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/101.png?raw=true
[image18]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/102.png?raw=true
[image19]: https://github.com/Ohara124c41/RoboND-PR2-3D_Perception/blob/master/README_images/100.png?raw=true



# Required Steps for a Passing Submission:
1. Extract features and train an SVM model on new objects (see `pick_list_*.yaml` in `/pr2_robot/config/` for the list of models you'll be trying to identify).
2. Write a ROS node and subscribe to `/pr2/world/points` topic. This topic contains noisy point cloud data that you must work with.
3. Use filtering and RANSAC plane fitting to isolate the objects of interest from the rest of the scene.
4. Apply Euclidean clustering to create separate clusters for individual items.
5. Perform object recognition on these objects and assign them labels (markers in RViz).
6. Calculate the centroid (average in x, y and z) of the set of points belonging to that each object.
7. Create ROS messages containing the details of each object (name, pick_pose, etc.) and write these messages out to `.yaml` files, one for each of the 3 scenarios (`test1-3.world` in `/pr2_robot/worlds/`).  [See the example `output.yaml` for details on what the output should look like.](https://github.com/udacity/RoboND-Perception-Project/blob/master/pr2_robot/config/output.yaml)  
8. Submit a link to your GitHub repo for the project or the Python code for your perception pipeline and your output `.yaml` files (3 `.yaml` files, one for each test world).  You must have correctly identified 100% of objects from `pick_list_1.yaml` for `test1.world`, 80% of items from `pick_list_2.yaml` for `test2.world` and 75% of items from `pick_list_3.yaml` in `test3.world`.
9. Congratulations!  Your Done!

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it!

### Exercise 1, 2 and 3 pipeline implemented
#### 1. Complete Exercise 1 steps. Pipeline for filtering and RANSAC plane fitting implemented.
---
The pipeline consists of compiling these filters/algorithms:
1. Voxel Grid filter
2. PassThrough filter
3. RANSAC plane segmentation
4. Extract inliers (run PCD)
5. Extract outliers (run PCD)
6. Using Point Cloud services

Of these, the `RANSAC algorithm` is a very common in computer vision and robotics. It works as an iterative method for `estimating parameters` based on data from mathematical models. It is very robust and has a ***high probability*** of supplying accurate estimations (also great for recursive `state estimation` applications).

Below are some images from after applying the filters listed above. More can be compiled using the `PLC_Viewer` and loading the appropriate `PCD` file.
![alt text][image1]

![alt text][image2]

![alt text][image3]




#### 2. Complete Exercise 2 steps: Pipeline including clustering for segmentation implemented.  
---
Integrating the pipeline for the first exercise allows us to quickly implement `clustering` using `ROS and PCL` after publishers and topics are created. The goal is to remove the table, so the objects can segmented for training in SVM in exercise 3. After tabletop segmentation and clustering is complete, the next step is to assign a color to each object. This is best done using random values in the `XYZRGB point cloud.` All of the necessary functions can be derived from the `PCL Helper` file. An image from successfully assigning and publishing to the cloud is below.   
![alt text][image5]
#### 3. Complete Exercise 3 Steps.  Features extracted and SVM trained.  Object recognition implemented.
---
##### Sensor Stick Fun:
Now, it is time to train our SVM. This is done by initializing the `capture_features` python file while using `roslaunch` to load the training file. The next two images show the `sensor_stick` rotating and analyzing each object (like a 3D scanner) in order to be able to identify it later.

![alt text][image6]

![alt text][image7]

##### Histogram Data Analysis:
As can be seen, the first trial-and-error experiments did not result in nominal values (.77 accuracy for the plastic cup). However, the accuracy is normalized in a weighted manner, so the resulting cumulative accuracy was 90%

![alt text][image8]

![alt text][image9]

![alt text][image10]


### Pick and Place Setup

#### 1. For all three tabletop setups (`test*.world`), perform object recognition, then read in respective pick list (`pick_list_*.yaml`). Next construct the messages that would comprise a valid `PickPlace` request output them to `.yaml` format.
---
##### The Real Fun:

Next, we have three different situations in which to analyze objects. The core requirements include eight different objects.

Make sure that you change the `test.world` value in order to have the correct item list rendered into the environment. Similarly, make sure that all of your bin numbers are consistent, or the compiler will result in a "shape cannot be created" error, due to the spatial discrepancies.


 The eight objects to detect (and place):
- biscuits
- book
- eraser
- glue
- snacks
- soap
- soap2
- sticky_notes

![alt text][image4]

![alt text][image11]

![alt text][image12]

##### Histogram Data Analysis:

This time, the results were much better. Since trial-and-error was taking too long from initialization to the histogram plot, the Slack channel was consulted in order to find suitable `leaf size, axes intercepts, k-values and cluster size.` However, there was no consensus on what the best choices were, and different students could get excellent results with very different setups. These parameters are implemented in the `pcl_callback()` but are the most noticeable here, as human perception is not good at distinguishing small visual differences. A small change in one parameter can have drastic impacts on the result of the program. The following values result in the displayed in images:

parameter | values
----|-----
leaf size | 0.01
z-min | 0.5
z-max  | 0.9
x-min  |  0.3
x-max  | 1  
bins  | 32
k  | 50
x  | 1
tolerance  | 0.02
clust-min  | 100
clust-max  | 2000

![alt text][image13]

![alt text][image14]

##### Results:

The robot was able to correctly identify all of the objects. However, the hardware the VM ran on was not powerful enough for optimal performance, even with increasing the memory and number of processes to the maximum available in the VM settings. This resulted in the robot moving very slowly (or not at all) once the `point cloud` had been initialized. The image recognition would not begin until the point cloud was loaded.

One method of addressing this was to run the `roslaunch` of the world spawning file and waiting several minutes to initiate the project server file. However, sometimes this would crash and `catkin_make` and the `export reference` file needed to be re-initialized. As can be seen in test.world2 image, the Rviz program had crashed, though it was able to report the values. Likewise, in all cases, the object name would only appear for a few moments before de-rendering.

In test.world3, more images meant more memory, presumably, since the objects would not load unless `pcl_cluster` was selected as a `Topic` and the `boxes style` was used. Using the recommended `flat squares` choice resulted in objects not appearing or either Rviz/Gazebo freezing and/or crashing.

Nonetheless, the objects were properly identified. This will be improved in the near future, for more appealing write-up (though some roboticist like to see where the scholar had issues and how they brute-forced their way through them).
![alt text][image18]

![alt text][image17]

![alt text][image16]


Spend some time at the end to discuss your code, what techniques you used, what worked and why, where the implementation might fail and how you might improve it if you were going to pursue this project further.  
##### Issues

Many times, Rviz or Gazebo would randomly crash or give the response that it was "out of memory." This would lead to some features loading properly, while others might not compile at all. Another problem with this (as can be seen in the image related to the second test world) is that Rviz might crash, while the server is still running and reporting data.

Other times, `export GAZEBO_MODEL_PATH=~/catkin_ws/src/sensor_stick/models` would need to be called again, even though nothing had been changed since it's last recall. Similarly, `catkin_make` would need to be re-initialized, even though nothing had been added or removed to the files (not even a change in a .py file).

(Pick-n-place operation) the robot can often grab items, but with little respect to the surrounding environment. That means, it might plow through some items (it does not always come from above), flinging biscuits off the table so it can get a beer. Another issue, is sometimes it gets the item to the bin, but does not release until it has begun its next process, which results in dropping the item on the floor on the way back to another item.

Sometimes the robot would get confused if the `for loop` was not properly implemented. Basically, it would repeatedly attempt to identify objects or think every object was a beer (see below).

![alt text][image15]

##### Further Enhancements

1. Fix the hardware issues that result in Rviz crashing
2. Find better values for the filter axes, k-means, cluster size range, etc.
3. Complete 'Challenge Mode'
4. Improve the PR2's ability to actually pick-n-place objects (it likes to throw them off the table)
