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

The pipeline worked quite well with solidWhiteRight.mp4, with minor jitter for the broken-line left lane. For solidYellowLeft.mp4 however, jitter was noticeable and frequent, with some frames departing wildly from the expected result.

The arbitrary 25.0° and 50.0° slope windows would present problems if the vehicle was on a relatively sharp curve, or if the vehicle was not aligned in its lane.

Also another shortcoming would be loss of the drawn lanes if the broken road lines were even shorter, or had low visibility or contrast compared to the road.


### 3. Suggest possible improvements to your pipeline

The most important improvement to the pipeline is to include historical metrics. Since the current pipeline only processes individual frames without considering past frames, jitter was quite considerable. A weighted windowing function should be implemented. The polyfit coefficients should be compared and influenced by its past values. A quick solution, without weighting, is to just discard the newly computed coefficients for the current frame if it changed drastically from the previous one. If say the current computation is 20° off from the previous frame which happened only 1/30 seconds before, then that value should be discarded and the previous value substituted for it.

A better curve fitting routine should also be used instead of just a linear one, especially for challenge.mp4. The Bézier, polynomial, elliptical and other curve fitting features of OpenCV or NumPy will be considered for this improvement. For any curve fitting technique chosen, the algorithm should be revised so that lines closer to the vehicle (bottom of frame) would have more weight or influence than those far away. Unlike the current pipeline, each lane should also be compared to the other. Divergence of direction of the lanes would indicate a problem with the pipeline.
