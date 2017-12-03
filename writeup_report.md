## Advanced Lane Finding Project

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

[image1]: ./output_images/calibration_image_corners.JPG  "Calibration Image with Founded Corners"
[image2]: ./output_images/distortion_corrected_2.JPG  "Undistortion calibration images"
[image3]: ./output_images/distortion_corrected_test_image.JPG "Undistortion test images"
[image4]: ./output_images/combine.JPG "Binary Example"
[image5]: ./output_images/src_dest.JPG "src dest Example"
[image6]: ./output_images/warped.JPG "Warp Example"
[image7]: ./output_images/find_lane.JPG "Find Lane"
[image8]: ./output_images/laneboundary.JPG "Identified Lane"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  

You're reading the writeup. Please also see the IPython notebook - "Advanced-Lane-Finding.ipynb" which has the detailed annotation I put along with the code.

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the IPython notebook - "Advanced-Lane-Finding.ipynb", in preprocess class, step 1 - Calibrating Camera.

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image. See below as an example.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][image1]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the calibration image using the `cv2.undistort()` function and obtained this result: 

![alt text][image2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

With the calculated camera calibration matrix (mtx) and distortion coefficients (dist), I used the OpenCV function: undistort() to perform the image undistortion. This function remove distortion of image and output the undistorted image. Below is an exampleof applying distortion correction to raw images. See more details and examples of undistortion in ipython notebook Step 1.

![alt text][image3]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

After image undistortion, you may choose to do binary images first or perspective transform first. In this project I choose to do perspective transform first so the image here has already become "birds-eye view". You may see details in item #3 below before you read this item #2. 
For creating thresholded binary images, as the code in combined_binary() function in Preprocess class, I used combination of the L-channel of LUV and the b-channel of Lab to create a stacked thresholded binary image.  You may see further code detais in show_threshold_plot() function in preprocess class and more other test results in step 3 in ipython notebook. Here's one of examples that output from this step.

![alt text][image4]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

As mentioned above, in this project I choose to perform perspective transform right after I undistorted the image. The code for my perspective transform includes a function called `birds_eye()`, which appears in "Preprocess class & Step 2: Perspective transform."  The `birds_eye()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I used the get_src_dest_warp_points() function to the source and destination points:

![alt text][image5]

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image6]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then with the thresholded combined binary images, now I can detect lane lines pixels by using Peaks in a Histogram & Sliding window Search and fit my lane lines with a 2nd order polynomial. See details in fill_lanes () function in Advanced-Lane-Finding.ipynb - Step 5: Finding and Filling The Lane. Some breif descrpiton of steps:
- Take a histogram along all the columns in the lower half of the image.
- Use sliding window search (code referenced from lesson) in the first frame to locate the starting point and the lines. After 1st search then the seatch can start from the rough area, meaning don’t have to do slide window search method again on the new frame.
- Fit a polynominal to the found lane line pixels.

Example result:

![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I implemented the code of lane curvature and vehicle position calculation in get_radius_and_deviation() function in Advanced-Lane-Finding.ipynb - Step 4 cell. I referenced the code from the lesson & the equation of calculating radius from: http://www.intmath.com/applications-differentiation/8-radius-curvature.php.


#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this visualization step in my code in Advanced-Lane-Finding.ipynb - Step 5 (close to bottom) & Step 6.
The code in bottom of Step 5 is to warp the detected lane boundaries back onto the original image and Step 6 is to visualize the result. Here is an example of the result on a test image:

![alt text][image8]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./result_video.mp4)
You may found further details in  Advanced-Lane-Finding.ipynb - Video Processing Pipeline.
---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
