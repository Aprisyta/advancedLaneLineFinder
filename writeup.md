## Advanced Lane Finding

### Project 2 - Self Driving Car Nanodegree

---

**Advanced Lane Finding Project**

This project aims to detect the lanes from the video stream and shade the areas between the identified lanes.

The goals / steps of this project were to:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/chessCalibrationImage.jpg "Undistorted"
[image2]: ./output_images/undistortedImageOftest3.jpg "Road Transformed"
[image3]: ./output_images/binaryImage.jpg "Binary Example"
[image4]: ./output_images/warpedImage.png "Warp Example"
[image5]: ./output_images/linesFittedImageOftest3.jpg "Fit Visual"
[image6]: ./output_images/resultOftest3.jpg "Output"
[video1]: ./test_videos_output/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

The motive of this project was to detect lane lines using camera, OpenCV and Python.

### Camera Calibration

The code for this step is contained in the second code cell of the IPython notebook located in ".advancedLaneLineFinding.ipynb".

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline

A pipeline was developed to detect lane lines. First this pipeline was tested on set of images. As video is just a compilation of images, then the video was passed to same pipeline in frames.

#### 1. Distortion Correction

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2]

Once I had the distortion coefficient from the camera calibration, I applied the same to the images. 

The code and all the images are provided in my Jupyter NB section has been save in output_images folder as undistortedImageOf*.jpg.

#### 2. Color transforms and gradients to create threshold binary image

I defined and experimented with a number of functions for gradient and color thresholding including:
- RGB-to-grayscale
- Sobel x and Sobel y gradient threshold
- Thresholding on total gradient magnitude 
- Thresholding on directional gradient 
- Gradient direction thresholding
- RGB-to-HSL conversion 

I found that combination of L-channel and S-channel along with directional gradient and sobel x and sobel y worked the best. S channel does an amazing job of ignoring the shadows in the image. The code for the same is present in cell 4 of code. My output of the binary image is:

![alt text][image3]

#### 3. Perspective Transform

The code for my perspective transform is in function called `perspectiveTransform()`, which appears in cell 5 of the IPython notebook.  The `perspectiveTransform()` function takes as inputs an image (`img`).  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[600,460],[740,460],[1100,700],[250,700]])
dst = np.float32([[300,0],[1000,0],[1000,720],[300,720]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Detect lane-line pixels and find lane boundary

I plotted a histogram to detect where the maximum concentration of pixel exists. There were 2 such parts (where the lane line exists), one on left and right. This conversion is present in cell 7. I used sliding window method to detect the lines and fit them in a 2nd degree polynomial. My fitted lines looked like:

![alt text][image5]

#### 5. Radius of curvature of the lane and the position of the vehicle with respect to center.

To detect Radius of Curvature and position of vehicle, I had to map the pixel points to real world distance in x and y plane, assuming lane length of 30 m and lane width of 3.7 m each divided by the respective pixel distances in the image (720 and 700). The code for the same can be found in cell 8 and cell 9.

To calculate radius of curvature, following formula was used
`R = (1+(2Ay+B)2)3/2 / |2A|`

#### 6. Detected Lanes

For unwarping the lines to the original image, I used the code provided in the course. The code is provided in cell 9 in function `unwarp`.

![alt text][image6]

---

### Pipeline (video)

Here's a [link to my video result](./test_videos_output/project_video.mp4)

---

### Discussion

#### 1. Issues in the Project

- Line detection can be better. Often there in glitch in detection of lane lines when run on challenge_video
