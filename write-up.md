## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

First thing we have to do, to even warp and unwarp the images, is to get the camera calibration matrix and the distortion coefficents;
and to do that, we should first define the number of rows and columns of the chessboard. 
the algorithm goes like this, we map the corners of our image/shape, detect the corners to be precise and map them to a corret-image.

this calculates the following : camera matrix, distortiong coefficients, retrieval value, rotational vectors and transition vectors.

so it is like telling our machine, we want these points to be in this position, by doing that, it gets undistorted into a natural/original/correct image. 

![Chessboard-Undistorted](/HLS&LAB-Sliding/undistort-camera_cal-output/calibration1.jpg)

#### 1.1. Provide an example of a distortion-corrected image.

Second step, is what we actually did the camera calibration for. we pass our parameters, the parameters we gain from last step; camera matrix, distortion matrix
ret to correct the distorted image. because well, using these matrices we know which point should be where. 

![Road-Undistorted](/HLS&LAB-Sliding/undistort-output/straight_lines2.jpg)

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image. 

I, myself,  have tried variety of methods regarding this step, thanks for the tip I recieved, combining HLS&LAB or LUV&LAB is best, though it really needs
complicated tuning. 

The b channel works very well and picks yellow lines very good. combined with L channel from LUV or HLS it gets the other part, which is white lanes.

it goes like this; we take out the other channels and only work on the LAB B-channel values. then we further threshold the LAB B-Values and-
 apply sobel to the HLS L-cahnnel. 
 
![Road-Binary](/HLS&LAB-Sliding/HLS&LAB-output/straight_lines1.jpg)

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.
At this step, we have to define our area of interest, the 4 corners of an image we want to warp and focus on. 
Now, all we have to do, is pass on the image to wrap ( binary and another for coloured for the fun! ) 
number of rows and columns on the calibrated image, camera matrix and distortion coefficents. 
then define the destination of these corners in the new(warped) image. 

this is all we need to apply prespective transform (bird-eye-view in our case) 
now we can calculate the transformation matrix.

for future needs, we also calculate the Inverse of this matrix, for when we need to transform back again(unwarp).

![Road-Binary-Warped](/HLS&LAB-Sliding/test_images_binary-bird-output/straight_lines1.jpg)
![Road-Warped](/HLS&LAB-Sliding/test_images-bird-output/straight_lines1.jpg)

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

for this approach, I have used the Sliding-Window-Search;
we define number of windows, for how many slides to do, window width, height, margin for space around pixel,
and number of pixels we want to add as centroids for our windows. 

it goes like this, we know for our purposes, that lane lines are near the car and mostly vertical or close to. Thus, we start by finding the pixels 
of a vertical or almost vertical lines in the bottom part-close to car. Then we start following these pixels upwards, if we detect the sufficient number-of
pixels in a window, we know that the mean value of these pixels is the value we want as center for our windows. 

and we keep on! till lane is found! 

using the output of the sliding windows, we use the centroids to fit a polynomial line which specifically identifies the curve of the lane line.
Depending on our values, of course. 

![Road-Warped](/HLS&LAB-Sliding/test_images-bird-output/straight_lines1.jpg)

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Now to get the radius in a real world, we just have to consider the actual value of pixels in the real world. how many pixels equal to how many meters,
for X and Y axis of course, which are different. 

then we use the provided equations to find such value. 

To get the car position, we work it out, using the values of our lanes and curvature, such way that we calculate where exactly we are. 
#CONSIDER A CIRCLE
The Center is simply the Width divided by two. 

then we simply can subtract the position and center values to get the position of car according to the center. 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

![Final-output](/HLS&LAB-Sliding/output_images/pipeline_image_test/straight_lines1-output.jpg)




---PLEASE NOTE, There are many submissions and codes. I just like to keep them as they might help us further with the review; show how it become better and
---more needs to be done. I have used many approaches, HLS-Sobel, RGB-Magnitude-Direction, LUV&LAb and HLS&LAB.
--- SO, FOR FINAL RESULT, PLEASE CHECK THE OUTPUTS IN HLS&LAB-Sliding or even LUV&LAB they are almost identical. 

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Thanks for the reviews again! 
Hope the position and center are fixed now !