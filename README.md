# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline, as discussed in the earlier lessons, consisted of doing for each frame grayscaling, Gaussian blur, Canny edge detection, masking, and the Hough transform.

The newest challenge is to identify and draw single left and right lanes. My process was done in several steps:
1. For the left lane, I looked for the longest segment returned by HoughLinesP() that had an angle between 25.0° and 50.0° relative to the x-axis. For the right lane, the angles were from -25.0° and -50.0°.
2. The other shorter segments were then compared to each longest segment. If the difference was less than or equal to 8.0°, that segment was chosen to 'cluster' with the long segment. Other segments were discarded.
3. Each cluster of segments was processed through a weighted linear polyfit routine. Weighting is important or else short segments will be considered as influential as longer segments which should not be the case. The weighting is done via linspace(). The result is an array of points (actually 4 arrays, 1 each for x and y for each lane). A segment that has for example a length of 100 pixels would have 100 points in the array.
4. The arrays are passed to polyfit() with a degree of 1, or linear. It is basically a linear regression of all the points in the qualified lines returned by the Hough transform.
5. Polyfit returns the slope and intercept of the regression. Those coefficients were then used to calculate the x values for each lane's end point. The y-values were the height of the frame and y-coordinate of the top of the mask.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
