# **Finding Lane Lines on the Road** 

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds lane lines on the road first on a different set of images and subsequently on a video. The key concept is that the video is considered as a stream of images.

* Reflect on your work in a written report

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

I first decided to implement and test my pipeline on one (whiteCarLaneSwitch.jpg) of the images provided in the project. After obtaining a satisfying tuning for that image I tested my algorithm on all the other images by using a for loop to scan all the provided images.

My pipeline consisted of 6 steps. 

1) First, I converted the image to gray-scale, so that rapid changes in brightness could be used to identify the edges of the image.

2) As second step a Gaussian filtering is applied on the selected image. This filtering aims to remove Gaussian noise from the image and smooth it. The selection of the kernel size has to be done with care because choosing it too high will remove not only noise but also useful information for the lines detection.

3) On the filtered image is then applied the Canny transform. This function tries to detect edges in the image by computing its gradient. With the gradient is possible to have a measure of how fast pixel are changing and also in which direction. The Canny transform will then select just the pixel with the strongest gradient. 

4) After that I extracted just the region in front of the ego-vehicle by making use of the provided function region_of_interest().
This function is using a polygon made of 4 vertices to select a precise area of the image.

5) As fifth step I applied the Hough transform to the image provided by the Canny transform. The output  of this function is a set of straight lines found on the image.

6) The final step of the pipeline is to overlap the final processed image with the original image. In this way is possible to conclude whether the algorithm has performed well or not.

NOTE: An example image has not been inserted here because all the above steps are also highlighted with comments and related images in the file P1.ipynb.


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by adding the following steps:

- First I evaluated the slope of each of the lines provided by the Hough transform. In this way, based on the sign of this parameter I could evaluate whether I was dealing with a left or a right line.

- Then, for each left and right line I computed the average x and y coordinate and the average slope. Based on this information I was able then to compute the q intercept of the line.

- As third step, with all the information above, I evaluated the x coordinate of the farthest and the closest point (from the ego vehicle). They are respectively obtained from the following y-coordinate:
    - The y value used to select the region of interest
    - The maximum y coordinate. 

- As final step I called the cv2.line() function to connect these two points

### 2. Identify potential shortcomings with your current pipeline

One of the main shortcoming of this algorithm is that it might not be able to properly handle lane lines detection while the ego vehicle is performing a curve, especially curves with high curvature. This is due mainly to two reasons:

  1) After obtaining the detected lines the algorithm is just extending the detected line with a straight line to extrapolate the missing information. 
  
  2) The fact that we are selecting only a specific area in front of the ego vehicle implies that, while performing a tight curve, the lines might not be properly detected after a specific distance because they might be out from the selected region.

Another shortcoming is having a hardcoded value to detect the end of the line recognition. A better solution, that depends for example on the camera range should be preferred.

These are probably the reasons why the algorithm applied on the challenge video is not providing satisfactory results.

### 3. Suggest possible improvements to your pipeline

When one of the two lines is not present on the road this algorithm would produce no output for that line. A first simple potential improvement could be to use the information from the available line to reconstruct also the missing line.

Another potential improvement could be to extend the line detection also to neighboring lanes. 

Finally, it is also possible to notice that sometime the reconstructed line is jumping. This point could be improved by adding information of the previous step/steps for the detection of the current step.
