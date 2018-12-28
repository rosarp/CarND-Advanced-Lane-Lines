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

[image1]: ./output_images/undistorted-chessboard.png "Undistorted Chessboard"
[image2]: ./output_images/undistorted-test3.png "Undistorted Test3"
[image3]: ./output_images/threshold-1.png "After Threshold"
[image4]: ./output_images/threshold-2.png "After Threshold"
[image5]: ./output_images/test1-threshold.png "Test1 After Threshold"
[image6]: ./output_images/test2-threshold.png "Test2 After Threshold"
[image7]: ./output_images/test3-threshold.png "Test3 After Threshold"
[image8]: ./output_images/test4-threshold.png "Test4 After Threshold"
[image9]: ./output_images/test5-threshold.png "Test5 After Threshold"
[image10]: ./output_images/test6-threshold.png "Test6 After Threshold"
[image11]: ./output_images/straight-lines1-warped.png "Warped perspective Straight Lines1"
[image12]: ./output_images/test5-warped.png "Warped perspective Test5"
[image13]: ./output_images/test2-lines-marked.png "Test2 with lines marked"
[image14]: ./output_images/test2-with-curvature-and-lines-marked.png "Test2 with lines marked"
[video1]: ./processed-project_video.mp4 "Project Video"
[video2]: ./processed-challenge_video.mp4 "Challenge Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./P2.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the 3D world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

Exception to the matrix 9x6 are 3 other images. One found with 9x5 matrix `calibration1.jpg` & others were not balanced matrices from right to left. (viz. `calibration4.jpg` & `calibration5.jpg`

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
At `Applying threshold to image for lane detection` section:

I used a combination of color and gradient thresholds to generate a binary image.  Here's an example of my output for this step.  

![alt text][image3]
![alt text][image4]

I used S from HLS & R from RGB to get better results.

After applying same on all 6 test images. Below is the result.

![alt text][image5]
![alt text][image6]
![alt text][image7]
![alt text][image8]
![alt text][image9]
![alt text][image10]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

In the 8th code cell in section `Perspective warping to see image from different perspective`, the code for my perspective transform appears which includes a function called `warp_perspective()` The `warp_perspective()` function takes as inputs an undistorted image (`image`), as well as source (`pt`) which is output of another function `get_perspective_transform`.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32(
    [[(img_size[0] / 2) - 55, img_size[1] / 2 + 100],
    [((img_size[0] / 6) - 10), img_size[1]],
    [(img_size[0] * 5 / 6) + 60, img_size[1]],
    [(img_size[0] / 2 + 55), img_size[1] / 2 + 100]])
dst = np.float32(
    [[(img_size[0] / 4), 0],
    [(img_size[0] / 4), img_size[1]],
    [(img_size[0] * 3 / 4), img_size[1]],
    [(img_size[0] * 3 / 4), 0]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 206, 718      | 206, 718      | 
| 586, 457      | 206, 0        |
| 696, 457      | 1106, 0       |
| 1106, 718     | 1106, 718     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.


![alt text][image11]
![alt text][image12]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

With function `fit_polynomial` from notebook, I calculated left & right fit using polyfit function.
Then using left_fit & right_fit I calculated windows with `search_around_poly` function.
Fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image13]
![alt text][image14]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in code cell no. 11 in Nodebook with function `get_radius`. 
I calculated displacement in `search_around_poly` function itself, where I can access lane center value and image center value.

![alt text][image14]

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Above `Measuring curvature` section, I implemented this step.
Here is an example of my result on a test image:

![alt text][image14]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./processed-project_video.mp4)
Here's a [link to my video result](./processed-challenge_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

In `search_around_poly` function, I found `left_fitx` & `right_fitx` to calculate top & bottom lines. I generated points with linspace and passed it to fillpolly to fill the area of lane.

I mainly concentrated on filtering noise through applying color, gradient thresholds. 
After that, I calculated hostogram data and marked starting points of left & right lanes.

It will fail with glass reflection from the windshield. 

I faced difficulty in getting polygon colored, which also took my lot of time.

I will work on sliding window & will also use history to calculate from past frame's lane data to improve performance.
Also, will improve lane line color.