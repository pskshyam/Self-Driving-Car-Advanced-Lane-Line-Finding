**Advanced Lane Finding Project**

---

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

[image1]: ./output_images/ChessboardCorners/Undistorted.jpg "Undistorted"
[image2]: ./output_images/Undistorted/test1.jpg "Calibrated"
[image3]: ./output_images/ThresholdedBinary/test1.jpg "Binary Example"
[image4]: ./output_images/Birds-eyeView/test1.jpg "Perspective Transformed"
[image5]: ./output_images/SlidingWindows/test1.jpg "Sliding Windows Example"
[image6]: ./output_images/LaneLines/test1.jpg "Fit Lane Lines Visual"
[image7]: ./output_images/Curvature/test1.jpg "Output"
[video1]: ./output_tracked.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

The code for this step is contained in the [2,3] code cells of the IPython notebook located in "./P2.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection. All chessboard images with corners drawn are placed in "./output_images/ChessboardCorners/".

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to one of the calibration images using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Distortion Correction

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

I used the distortion coefficients and calibration matrix obtained in the Camera Calibration step above to undistort actual test images of the vehicle. The code for this step is contained in the creation of pieline, code cell [7], under heading of "### Apply Undistortion ###" of the IPython notebook located in "./P2.ipynb". All undistorted images are placed in "./output_images/Undistorted/".

#### 2. Creating thresholded binary image

I used a combination of color (s-channel and v-channel of HSV) and gradient (sobel-x and sobel-y operators) thresholds to generate a binary image. The code for my thresholded binary image includes functions, `abs_sobel_thresh()` and `color_threshold()` which appear in Helper Functions of code cell [5]. The code for this step can be found in the creation of pipeline, code cell[7], under heading of "### Apply Gradient and Color transformations to create binary image ###" of the IPython notebook located in "./P2.ipynb".  Here's an example of my output for this step. All thresholded binary images are placed in "./output_images/ThresholdedBinary/".

![alt text][image3]

#### 3. Perspective transformation

The code for my perspective transformation includes functions, called `getPerspectiveTransform()` and `warpPerspective()`, which appears in the creation of pipeline, code cell [7], under heading of "### Apply Perspective Transformation to the image to get birds-eye view ###" of the IPython notebook located in "./P2.ipynb". The function `getPerspectiveTransform()` takes as input source (`src`) and destination (`dst`) points and gives transformation matrix (`M`) as output. The function `warpPerspective()` takes as input an image (`img`) and transformation matrix (`M`) and image size (`img_size`) and gives warped image as output. I chose the source and destination points in the following manner:

```python
src = np.float32(
     [[img.shape[1]*(.5-mid_width/2), img.shape[0]*height_pct],\
     [img.shape[1]*(.5+mid_width/2),img.shape[0]*height_pct],\
     [img.shape[1]*(.5+bot_width/2),img.shape[0]*bottom_trim],\
     [img.shape[1]*(.5-bot_width/2),img.shape[0]*bottom_trim]])
des = np.float32(
      [[offset,0], 
      [img_size[0]-offset, 0], 
      [img_size[0]-offset, img_size[1]], 
      [offset, img_size[1]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 589, 446      | 320, 0        | 
| 691, 446      | 960, 0        |
| 1126, 673     | 960, 720      |
| 154, 673      | 320, 720      |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

All images with Birds-eye view are placed in "./output_images/Birds-eyeView/".

![alt text][image4]

#### 4. Identifying lane-line pixels

The code for identifying lane-line pixels can be found in the creation of pipeline, code cell [7], under heading of "### Finding pixels using convolving sliding windows ###" of the IPython notebook located in "./P2.ipynb". 

I have used convolving sliding windows approach to find lane-line pixels. This includes a function called `find_window_centroids()` which appear in Helper Functions of code cell [5]. It takes as input warped image (`warped`), width and height of sliding window (`width`, `height`), margin, smoothing factor and x & y pixel-meter ratios and gives window centroids as output.

I verified that my sliding windows function was working as expected by using a function `window_mask()` to draw the window areas onto the warped image and used left and right pixel points for each window stored in lists `lpoints` and `rpoints`.

All images with sliding windows are placed in "./output_images/SlidingWindows/".

![alt text][image5]

#### 5. Fitting lane-lines

The code for fitting lane-line pixels can be found in the creation of pipeline, code cell [7], under heading of "### Fitting Lane Lines ###" of the IPython notebook located in "./P2.ipynb". 

I have used the window centroids to fit a second-degree polynomial and calculated left and right polynomials `left_fitx` and `right_fitx`. Then I have found the left and right lanes `left_lane` and `right_lane`.

I verified that my lines fitting was working as expected by drawing the left and right lanes `left_lane` and `right_lane` onto the road. All road images with Lane-Lines fitted are placed in "./output_images/LaneLines/".

![alt text][image6]

#### 6. Calculating radius of curvature and position of the vehicle

I did this in the step for creation of pipeline, code cell [7], under heading of "### Finding radius of curvature ###" of the IPython notebook located in "./P2.ipynb". 

I have found the curvature by fitting second order polynomial to the left pixel positions and used the polynomial coefficients in Radius of Curvature equation to get the radius. Then, calculated the center of the two lines by averaging them and the position of the vehicle with respect to center.

#### 7. Final Result

Finally, I have plotted the lane lines on road images with radius of curvature, position of the vehicle drawn on top of them.

I implemented this step in creation of pipeline, code cell [7], under heading of "### Draw the text showing curvature, offset and speed ###" of the IPython notebook located in "./P2.ipynb". Here is an example of my result on a test image:

![alt text][image7]

---

### Pipeline (video)

Here's a [link to my video result][video1]

---

### Discussion

The most challenging part of this project is identifying color and gradient thresholds such that the lane lines are clearly visible in any condition of the road and accurately identifying the trapezoid measurements of the camera view for transforming the image into a birds-eye view.

The pipeline is likely to fail when there are sharp and blind curves or when there are single lane (no lanelines) roads. Another shortcoming could be identifying lines in different conditions like darkness, fog, rain, etc.

To improve the pipeline to resolve any such cases, we should introduce some dynamic aspects where lanelines or road itself can be found dynamically.