**Advanced Lane Finding Project**

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

[image1]: ./camera_cal/calibration1.jpg "original distorted"
[image2]: ./output_images/2-undistorted_images/calibration1.jpg "undistorted"
[image3]: ./test_images/test5.jpg "distorted street view"
[image4]: ./output_images/2-undistorted_images/undistorted1.jpg "undistorted street view"
[image5]: ./output_images/3-thresholded_images/thresholded1.jpg "thresholded street view"
[image6]: ./output_images/4-warped_images/warped1.jpg "warped image"
[image7]: ./output_images/5-lane_lines_extracted/lines_extracted1.jpg "warped image"
[image8]: ./output_images/6-Lane_lines_polyfitted/poly_fitted1.jpg "warped image"
[image9]: ./output_images/8-curvature_measred_based_on_the_left_line/left_curve_measured1.jpg "warped image"
[video1]: ./project_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in the file called `step1.py`.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 
![][image2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I described how I applied the distortion correction to one of the test images like this one using "cv2.undistort(img,mtx,dist,None,mtx)":
![][image3]  ![][image4]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at lines 23 through 40 in `main.py`) and using some helping functions in 'step2.py' file.  Here's an example of my output for this step.

![][image5]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `changePerspective()`, which appears in the file `step3.py`.  The `changePerspective()` function takes as inputs an image (`img`), as well as source points in a dictionary called (`trapizoid`), the offset from the edges of the wraped image, and a flage to calculate the inverse if True.  I chose the hardcode the source and destination points in the following manner:

```python
img_size=(img.shape[1],img.shape[0])
src=np.float32([[trapizoid['first'][0],trapizoid['first'][1]],
                    [trapizoid['second'][0],trapizoid['second'][1]],
                    [trapizoid['third'][0],trapizoid['third'][1]],
                    [trapizoid['forth'][0],trapizoid['forth'][1]]])
offset=img_size[0]*offset_pct
dst=np.float32([[offset,0],
                [img_size[0]-offset,0],
                [img_size[0]-offset,img_size[1]],
                [offset,img_size[1]]])
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 590, 445      | 320, 0        | 
| 690, 445      | 960, 0        |
| 1126, 673     | 960, 720      |
| 153, 673      | 320, 720      |

I verified that my perspective transform was working as expected by getting a warped image and make sure the 2 lines are parallel 

![][image6]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

There is a class called tracker in the file 'tracker.py' that do all the line tracking using the provided convolution method and fit my lane lines with a 2nd order polynomial and draw them in the fuction called 'drawLines()' file 'step4.py'  kinda like this:

![][image7]  ![][image8]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in line 63 in 'main.py' using a function in 'step5.py'

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines 59 through 70 in my code in `step4.py` in the function `drawLines()` and wrote the radius in function'writeOnFrame()' in file 'step6.py.  Here is an example of my result on a test image:

![][image9]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took:  
1- followed the steps  
2- verified every step before proceeding  
3- when i got stuck really bad i watched the walkthough video of the project  
###what techniques I used:  
1- calibration using the chess board  
2- combining the outputs of gradient x, gradient y, color thresholding: V channel in HSV, S channel in HLS  
3- change perspective using fixed points  
4- the convolution method to find the lines  

what worked and why:  
1- actually i saw that using the v channel is the same as L in HSV (or maybe i didn't catch the differences), but the s channel was really robust against brightness  
2- the provided methods for finding the line were almost the same, so i chose the convolution one because it was the easiest for me  

where the pipeline might fail and how I might improve it if I were going to pursue this project further:  
1-the pipeline will fail if the lane lines went out of the defined trapizoid , to solve it i will think of a way to make these points adapt to the road conditions  
2- the pipeline will fail if there were frames with disappeared lane lines  
3- if the lane lines colors needed another range of thresholding, to fix this we can use previous infos like difference from the center of the lane and the width of the lane to predict where will the lane be in the next frame and it wasn't detected tweek the thresholding range  
