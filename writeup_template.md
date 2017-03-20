
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

[image1]: ./camera_cal/calibration1.jpg "Distorted"
[image2]: ./output_images/stage0/undistorted_calibration1.jpg "Undistorted"
[image3]: ./test_images/test2.jpg "Distorted"
[image4]: ./output_images/stage1/undistorted_test2.jpg "Undistorted"
[image5]: ./output_images/stage1/binary.jpg "Binary"
[image6]: ./output_images/stage1/channels.jpg "Channels"
[image7]: ./output_images/stage1/straight_red.jpg "Straight"
[image8]: ./output_images/stage1/straight_red_warped.jpg "Warped"
[image9]: ./output_images/stage1/left_line.jpg "Left line"
[image10]: ./output_images/stage1/right_line.jpg "Right line"
[image11]: ./output_images/stage1/project_test5.jpg "Lane"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Camera Calibration

The code for this step is contained in the first code cell of the Jupyter notebook located in ".P4-Advanced-Lane-Lineds.ipynb"

I start by preparing "objpoints", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

Original Image
![alt text][image1]

Undistorted Image
![alt text][image2]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.

To correct the distortion of the images of the camera during drive, I apply`cv2.undistort()` with the camera matrix and distortion coefficients calculated during camera calibration.
You can see the code in the cells 6 and 7 of the Jupyter notebook.

before correction:

![alt text][image3]

after correction:

![alt text][image4]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
To create a thresholded binary image I've used geometric, color and gradient masking.

In cell 9 of the Jupyter notebook, there is the `binarize()` method, it converts the image in HSL, and applies the
Sobel gradient in x direction on grayscale channels and on saturation.

As threshold for the channels I've used
S_TRESHOLD_LOW = 120
S_TRESHOLD_HIGH = 255

SX_TRESHOLD_LOW = 20
SX_TRESHOLD_HIGH = 255

L_TRESHOLD_LOW = 40
L_TRESHOLD_HIGH = 255

After experimenting with various values.

Here's an example of my output for this step:
Binary image
![alt text][image5]
Channels Image
![alt text][image6]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

To perform the perspective transform at cell 12 I choosed
the area defined by the follwing corners:

corners = np.float32([[190,720],[589,457],[698,457],[1145,720]])

The result can be saw analyzing the following images:

Straight:
![alt text][image7]
Warped:
![alt text][image8]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In cell 18, after processing the image with previous functions and considered the region of interest where lanes
are admitted, I try to visualize the lane lines with get_lane_from_window.



![alt text][image9]

![alt text][image10]

I've organized the info for the Lane line in a class
like suggested.
The line class is at cell 21

It contains the method to calculate the coefficients for the polynomial set_current_fit_xvals and set_avgcoeffs

I used a sliding window histogram of the pixel values to select pixels around the two peaks (one for each line).

After selecting the pixels, I fit a second-order polynomial to each line using `np.polyfit()`. I check if the detected lines are valid by making sure each horizontal pair of points on the left and right lines are within a certain range of pixels.

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The method to calculate the curvature is

set_radius_of_curvature

it performs this by averaging the curvature of the left and right lane lines. I calculate the position of the vehicle by measuring the distance from the center of the frame to the bottom of the left and right lines and taking the difference of those.  

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Applying the pipeline to an image gives the follow

result:

![alt text][image11]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./processed_project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I've applied the course suggested methods, I can see
this approach is good for the default simple video, but not
for the harder ones, containing noise with shadow and high
curvature variability. I think an approach mixed with P3 with deep learning could be useful to improve the lane finding.
