**Advanced Lane Finding Project**

The objective of this project are the following:

* Practice traditional computer vision techniques in lane finding still very beneficial to understand the problems despite Deep learning is heavily used in this field
* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


---


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./examples/example.ipynb" (or in lines # through # of the file called `some_file.py`).  

camera calibration involves three steps:
* preapre image point by using 'cv.findChessboardCorners'
* calculate camera matrix 'cv.calibrateCamera'
* undistort the image using calculated camera matrix by using 'cv.undistort'
I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Distorsion correction.

By using the camera calibration restult distortion correction is applied to each of the images:
![alt text][image2]

#### 2. Thresholding the images based on color channels and gradients 

#####2.1 color space selection 
By comparing RGB HSV HLS, etc, selected S channel because it is less sensitive to shaded area 

#####2.2 Gradients threasholding is combined with X directional and magnitude and dirrectional thresholding to captuere most relevant pixel to lane lines   
I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines # through # in `another_file.py`).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

#### 3. Perspectivve transform is applied to the image to warp image into over look position.so the line remain parallel
#####3.1 Identify warp source and destination to calculate distortion and undistortion matrix  
 The method to calculate perspective transformation matrix is `calc_perspective_matrix()`, by hardcoding `src` and `dst`, it will return with `M` the transformation matrix 

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 301, 650      | 350, 650        | 
| 1005, 650      | 850, 650      |
| 716, 469     | 850, 720      |
| 567, 469      | 350, 0        |

Drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Fit poly line for detected lane line pixel 

#####4.1  Lane line pixel is detected by searching for peaks in picel histogram, and use prior imformation to narrow searching area


#####4.2 Second order poly line is fited to the detected lane line pixel



#####4.3 Sanity check is conducted to compare to avg line  

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Lane line curvature calculation

To calculate the curvature, I used equation from this [link](https://www.intmath.com/applications-differentiation/8-radius-curvature.php)

#### 6. Unwarp the result into forward looking perspective and output result video.

To unwarp the image, the same `calc_perspective_matrix()` is used, only switch `src` and `dst`, so an inverse transformation matrix can be calculated

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./output_images/project_video_submission.mp4)

---

### Discussion

#### Improvement can make the algorithm more robus 
* One of the challenges is to detect the line pixels at all lighting conditions, it would be helpful if the threshold values can be dynamiclly changed due to global conditions, perhaps utlizing external sensor
* Perspective transform process should have more accurate calibration process


[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/combined_sobel_test1.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"
