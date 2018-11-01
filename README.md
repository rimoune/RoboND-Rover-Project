## Project: Search and Sample Return

**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook).
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands.
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/rover_image.jpg
[image2]: ./calibration_images/example_grid1.jpg
[image3]: ./calibration_images/example_rock1.jpg
[image4]: ./misc/results_image.PNG


### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.
The repository came with test and calibration images to get us started. I have renamed the folder with test images to test_data_original. The images from the recorded run were saved in test_data folder. Below is a display of a randomly chosen image from the test data provided in the repository.

![alt text][image1]

Also, please find below images included in the calibration folder, one where we can see the grid on the terrain and one with a rock sample.

![alt text][image2]
![alt text][image3]

In terms of functions added and modified, I have followed the same basic implementations as in the video walkthrough. Thus, we have one function (color_thresh) for discriminating between navigable terrain and obstacles, one to identify rocks (find_rocks) and a modified perspect_transform to exclude from the analysis what is not in the field of the camera. 

#### 1. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result.

In Process_image() function we apply all functions defined in the lesson and the ones discussed above. The objective is to update a world map one image at a time by superimposing a ground truth map of the environment (found in the calibration images folder) with the newly identified navigable terrain, obstacles and rocks. Given an image in input, we apply a perspective transform to have a top-down view of valid information of what the camera can see, color_threshold to identify navigable terrain, convert pixels value to rover-centric coordinate, then to a world coordinate. As implemented in the video walkthrough, obstacles updated the red channel of the world map, navigable terrain the blue channel and rocks all three channels.

After recording a manual run, I run the process_image() on test data and used the moviepy functions provided to create video output of the result. Please find the video in /output/test_mapping.mp4

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.

I have modified the perception_step() to include all functions calls as in the process_image() in the notebook. Only few changes were introduced, such as update 2 newly added features of RoverState object (sample_angles, sample_dists) with mean angle and min distance from a cluster of pixels identified as rock samples. I wanted to use these fields in the decision_step() to help steer towards rocks but it is still WORK IN PROGRESS. Also, the default rgb_thresh in color_thresh was changed from (160,160,160) to (180,180,180) to improve fidelity.
The decision_step() was kept as it is (it is WORK IN PROGRESS), the only change being right at the end, when we don’t have vision data ahead, to try and steer
 	Rover.steer = np.clip(-15,15)



#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

The simulation settings used for the autonomous run were:
Screen resolution: 1280 x 720
Graphic quality: Good
FPS: ca 26

Please find below few statistics resulting from the autonomous run:

Time: 104.8 s
Mapped:  40.7%
Fidelity:  61.1%

![alt text][image4]

This first attempt to map the environment autonomously can clearly be hugely improved. Points that need careful attention are: 1- better logic to assess when the rover is stuck and better decision logic to come out of it, 2- implement a logic to help the rover come out of a situation where it turn around in circle when in a "clearing", 3- implement some logic to reach the located rock sample with velocity at target almost 0, 4- moving towards the center of the map when all samples are collected
