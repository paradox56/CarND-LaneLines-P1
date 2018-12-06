#  Udacity Project 1 - Finding Lane Lines
Guang Yang 
- - - -

**Problem Formulation**
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report

**System Requirements**
* Ubuntu 18.04 / Mac OS Mojave (Older versions might also work, but are not tested)
* Python 3.5
* OpenCV 3.4.2
* Matplotlib 3.0.2
* Pillow 5.3.0

- - - -

**Approach**
In this project, we only work with video streams that come from a single front-facing camera on a car. Because a video stream is nothing more than a series of images that plays at a fixed frame rate,  the problem can be broke down to finding lane lines for each image (frame). 

Each image is nothing more than a 3-dimensional matrix that has 0-255 in each entry.  The three layers represent Red, Green and Blue respectively and image resolution defines width x height of each layer. 

The following image processing techniques are used in my approach:
* Step 1. Apply Grayscale on the original image to reduce it into a 2-dimensional matrix.    
![Gray Scale Image](https://github.com/paradox56/CarND-LaneLines-P1/blob/master/test_images_output/grayScaleImagesolidWhiteCurve.jpg?raw=true)
* Step 2. Apply Gaussian Blur to reduce image noise and produce a smooth image.    
![Smoothed  Gray Image](https://github.com/paradox56/CarND-LaneLines-P1/blob/master/test_images_output/smoothImagesolidWhiteCurve.jpg?raw=true)
* Step 3. Perform edge detection on the resulting image by using Canny edge detector.    
* Step 4. Define a region of interest by mask out unwanted area to be black (set the value to 0 for unwanted area).     
![Canny Edge Detection with Mask](https://github.com/paradox56/CarND-LaneLines-P1/blob/master/test_images_output/maskImagesolidWhiteCurve.jpg?raw=true)
* Step 5. Use Hough Line Transform to detect straight line segments in processed image.     
![Line Drawing](https://github.com/paradox56/CarND-LaneLines-P1/blob/master/test_images_output/lineImageNoWeightssolidWhiteCurve.jpg?raw=true)
* Step 6.  Draw lines on original image by linear fitting the line segments obtained from previous step.   
![Result](https://github.com/paradox56/CarND-LaneLines-P1/blob/master/test_images_output/lineImageWeightedsolidWhiteCurve.jpg?raw=true)


**Reflection**
### draw_line() function
After applying the image processing techniques that is shown above, I use draw_line() function to generate two solid lines by linear fitting each individual line segment from Hough Line Transform.  The draw_line() function is explained as the following:       

For each line segment that contains (x1,x2,y1,y2), I calculate its slope to determine whether the segment belongs to left lane line or right lane line.  Because the origin is on the upper left corner of each image, if the slope is negative, then the segment begins to left line. Otherwise, the segment belongs to right lane. If the detected line segment is vertical, I.e., the slope is minus infinity, it causes numerical error in the program. Therefore, I perform a check in if statement to discard any vertical line. Next, I want to discard any near horizontal segment by enforcing a minimum slope . (abs(slope) > min_slope) In this case, min_slope = 0.4

After filter out unwanted segments and correctly classify them for either left lane line or right lane line, I use a polyfit() function from numpy to find two coefficients for each lane line that minimizes the error between corresponding line segments. Finally, I plot left lane line and right lane line using those coefficients and 4 fixed points. 

### 2. Shortcomings of current algorithm

There are several shortcomings of this method. First, the linear fit does not work well for curved road. The algorithm will fail if we are driving in a roundabout. Second, the algorithm might not work well when the color difference is small. The canny edge detector calculates the intensity gradients within the image, therefore it will have a hard time to find edges if the color difference is small. 


### 3. Possible Improvements

A few possible improvements includes  includes using poly fit with higher degree of order. Other options include performing non-linear Support Vector Regression (SVR) by selecting appropriate kernel functions. In addition, the image enhancement might be the key.  I think I can perform high pass filtering on my original image before doing the canny edge detector to improve detection performance. 