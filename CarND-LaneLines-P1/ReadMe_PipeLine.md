# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Test differnt CV methods:
	- change color space
	- convolutional method ( gaussian, erosion ) 
	- edge detection ( Canny ) 
	- implement hough transform and find lines 
* implement the above method and add other process to a pipe line
* Identify the problems and Next steps 


[//]: # (Image References)

[image1]: ./Summary_image/Original.png "Original"
[image2]: ./Summary_image/gray_blur.png "Gray_blur"
[image3]: ./Summary_image/Canny_Maskedregion.png "Canny_Maskedregion"
[image4]: ./Summary_image/Hough_Original.png "Hough_Original"
[image5]: ./Summary_image/Hough_find_corner.png "Hough_Find_Corner"
[image6]: ./Summary_image/Hough_Final.png "Hough_Final"
[image7]: ./Summary_image/Cluster_Clean.png "Cluster_clean"
[image8]: ./Summary_image/Cluster_NotClean.png "Cluster_NotClean"
[image9]: ./Summary_image/Cluster_Example1.png "Cluster_Example1"
[image10]: ./Summary_image/Cluster_Example2.png "Cluster_Example2"



---

### 1. PipeLine Description.
The pipe line takes a series color image and it has the following steps to identify the lane lines
1. Load a series color image and make sure data type is correct 
![alt text][image1]
 
2. Convert to gray scale and reduce the noise
	* Reduce noise will help to eliminate false edges from edge detection
![alt text][image2]

3. Mask the image with polygon to further reduce ROI and Edge detection using Canny 
![alt text][image3]

4. Use Hough Transform to find lines from the edge points from Canny
	* 'max_line_gap' defined the maximum distance between segments that will be connected to a single line.
	*'min_line_len' defined the minimum length of a line that will be created.
Increasing these parameters will create smoother and longer lines

	* 'threshold' defined the minimum number of intersections in a given grid cell that are required to choose a line.
![alt text][image4]

5. Find the corner point for each side by searching the point in "left" and "right" region based on the image dimension
 ![alt text][image5]
 
 6. Extend the line to the polygon region boundary
 ![alt text][image6]

### 2. Potential shortcomings
1. the final line is found by search the point at each side ( "Left", "Right"), prone to noise. 
2. Hyper-Parameter in varies function need to be tuned, for example: 
	- Gaussian
	- Erosion
	- Canny edge detection 
	- Hough transform
3. Region based mask reduced ROI other part of the perception program need to compensate this loss

### 3. Effort to improve Pipeline
1. Noticed the "Raw" hough line has many over laps so there is a chance to cluster the overlapped lines and improve the "confidence" of the lines:
	*By push the "raw" hough lines thru a hough function and cluster by the hough point distance, the Pipeline consists of following steps : 
		- Push "raw" hough line to hough function ( implementation included in program)
		- Calculate sum of distance between each hough point to the rest in hough space, and record points within threshold 
		- Sort the distance and based on the voting to find the representative point from each cluster as the hough point
		- Return the hough line based on cluster distance and filter the overlapped cluster 
2. Showing some Cluster result and hough line from cluster:
 ![alt text][image7]
 ![alt text][image8]
 ![alt text][image9]
 ![alt text][image10]

3. Next Step: 
	* Notice the cluster reflect the hough line are noisy, out lier affect greatly in clustering process especially when using threshold to determing if a point is "close" or not
	* Improve the voting process by implementing RASAC maybe helpful to reduce the effect from outlier
 
 
 

