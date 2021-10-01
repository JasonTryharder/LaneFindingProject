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

General tips on different channels and colorspaces:

* RGB colorspace => R-channel is quite useful to find required lines in some conditions.
* HLS colorspace => While H-channel results in a noisy output, the S-channel is good to find 
* yellow line and in combination with gradients, it can give a great result.
* LUV colorspace => L-channel may be useful for the white line (range: 215 - 255)
* LAB colorspace => B-channel may be good for the yellow line (range: 145 - 200)

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
If interested, a [Spline model](https://www.cbica.upenn.edu/sbia/papers/381.pdf) can be used to detect more complex lane


#####4.3 Sanity check is conducted to compare to avg line  

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]

#### 5. Lane line curvature calculation

To calculate the curvature, I used equation from this [link](https://www.intmath.com/applications-differentiation/8-radius-curvature.php)

The distance from center is converted from pixels to meters by multiplying the number of pixels by 3.7/700:
30 meters is equivalent to 720 pixels in the vertical direction
3.7 meters is equivalent to 700 pixels in the horizontal direction.

```python         
		ym_per_pix = 30/720 # meters per pixel in y dimension
	        xm_per_pix = 3.7/700 # meters per pixel in x dimension
```
To calculate the center of car wrt center of the lane, refer to this [link].(https://discussions.udacity.com/t/where-is-the-vehicle-in-relation-to-the-center-of-the-road/237424)

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

### General tools 
Suggestion:
1. Export conda environment into environment.yaml file so that you can recreate your conda environment later while practicing on your own system. Use the following command -

`conda env export -f environment.yaml`

2. Use of assertions and Logging:
Consider using Python Mock object and [assertions](https://realpython.com/lessons/assertions/) for sanity testing - assertions are great for catching bugs. This is especially true of a dynamically type-checked language like Python where a wrong variable type or shape can cause errors at runtime

3. Logging is important for long-running applications. Logging done right produces a report that can be analyzed to debug errors and find crucial information. There could be different levels of logging or logging tags that can be used to filter messages most relevant to someone. Messages can be written to the terminal using print() or saved to file, for example using the [Logger module].(https://docs.python.org/3/library/logging.html) Sometimes it's worthwhile to catch and log exceptions during a long-running operation so that the operation itself is not aborted.

4. Here's a code snippet to build a widget for parameters fine tuning (for jupyter notebook only):
```python
from IPython.html import widgets
from IPython.html.widgets import interact
from IPython.display import display

image = mpimg.imread('path_to_your_image')
image = cv2.undistort(image, mtx, dist, None, mtx)

def interactive_mask(ksize, mag_low, mag_high, dir_low, dir_high, hls_low, hls_high, bright_low, bright_high):
    combined = combined_binary_mask(image, ksize, mag_low, mag_high, dir_low, dir_high,\
                                    hls_low, hls_high, bright_low, bright_high)
    plt.figure(figsize=(10,10))
    plt.imshow(combined,cmap='gray')

interact(interactive_mask, ksize=(1,31,2), mag_low=(0,255), mag_high=(0,255),\
         dir_low=(0, np.pi/2), dir_high=(0, np.pi/2), hls_low=(0,255),\
         hls_high=(0,255), bright_low=(0,255), bright_high=(0,255))
```
Where combined_binary_mask function returns binary combination of different color and/or gradient thresholds and `ksize`, `mag_low`, `mag_high`, `dir_low`, `dir_high`, `hls_low`, `hls_high`, `bright_low`, `bright_high`

are parameters for thresholding (if you use other, you can change this part of code).

As a result, you would see a widget such as below and be able to fine-tune parameters on the fly:

![alt text][image7]

This will also make it easy to visualize the impact of each parameter on the resulting binary image with defined combination of thresholds.

5. For the extremely curved lines, you can improve your sliding window algorithm by shifting the center of the next window in the direction of the curve.

Mapping long sections of the road (i.e ~50m) to the birds-eye perspective makes detection of distant points difficult. Taking ~30m section of the road should produce better results.

Also, if the radius of curvature decreases too fast(meaning that the line is becoming more curved), you can dynamically increase the width of the next window. On the other hand, when RoC increases(meaning the line is straight and we don't need too many windows), you can decrease the width and increase the height(decreasing the number of windows).

6. 




[//]: # (Image References)

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/combined_sobel_test1.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[image7]: ./examples/widget_example.png "widget_example"
[video1]: ./project_video.mp4 "Video"

