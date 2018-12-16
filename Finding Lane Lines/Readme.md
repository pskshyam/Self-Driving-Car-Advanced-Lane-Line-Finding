# **Finding Lane Lines on the Road** 

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of the below 5 steps. 
1. I converted the images to grayscale
2. Applied Gaussian Blur to smooth out the images
3. Applied canny edge detection to find edges in the images
4. Defined a region of interest by filtering out unwanted edges in the images
5. Used Hough Transform to find out lines
6. Drawn lines on the original image

In order to draw a single line on the left and right lines, I modified the draw_lines() function by
1. Finding slope and center with defined thresholds to avoid extreme slopes of both left and right lanes
2. Finding single left and right lanes by averaging all lines slope and center
3. Derived coordinates of the left and right lines
4. Drawn left and right lane lines on the image

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when there are sharp and blind curves

Another shortcoming could be identifying lines in different conditions like darkness, foggy, raining, etc.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to dynamically identifying region of interest (ROI) in any given image.

Another potential improvement could be to ...
