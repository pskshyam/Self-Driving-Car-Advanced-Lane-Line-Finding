# **Finding Lane Lines on the Road** 

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines in the given images
* Apply the same pipeline on a video - a series of images - to draw line segments onto the road
* Optimize the pipeline for averaging/extrapolating the line segments to map out the full extent of lane lines
* A written report on the approach followed


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Pipeline Description

My pipeline consisted of the below 5 steps. 
1. Converting the images to grayscale
2. Applying Gaussian Blur to smooth out the images
3. Applying canny edge detection to find edges in the images
4. Defining a region of interest by filtering out unwanted edges in the images
5. Using Hough Transform to find out line segments
6. Drawn lines on the original images

A function process_image is created with the pipeline code which takes an image as input and returns image with line segments drawn.

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
