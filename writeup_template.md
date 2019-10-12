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

[image0]: ./camera_cal/calibration1.jpg "Original"
[image1]: ./output_images/undistort_0.png "Undistorted"
[image2]: ./test_images/test1.jpg "Original Image of road"
[image7]: ./output_images/undistort_road_0.png "Undistorted Image of road"
[image8]: ./output_images/color_grad_road_1.png "Binary Image of road from gradient and color filtering "
[image9]: ./output_images/orig_road_0.png "Undistorted Image of road"
[image10]: ./output_images/orig_road_lines0.png "Lines overlay on undistorted image"
[image11]: ./output_images/orig_road_warped0.png "warped image"
[image12]: ./output_images/complete_pipeline_road_0.png "Complete pipeline Example (radius calculation here is fake..check the video)"

[video1]: ./test_videos_output/project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README


### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is available in the cell under the title **camera calibration** . Code provided has been inspired in what is available on the OpenCV docs and it's quite similar to the one shown in the lessons. I've started with the definition of the *object points* assuming z=0 and by using `np.mgrid` function to fill the array of the undistorted grid. Then I cycled through all the provided images (which the grd available in 2 of them wasn't recognized by the OpenCV `findChessboardCorners` method. In any case, all the other grids has been recognized so I was able to provide enough *object points* and *image points* to the `calibrateCamera` method. Here below you can find an example of the distortion correction application by using `cv2.undistort` with the parameters calculated by the steps described above:

![alt text][image0]
![alt text][image1]

### Pipeline (single images)

Starting from the original image below, I will show all the steps implemented in the pipeline

![alt text][image2]

#### 1. Provide an example of a distortion-corrected image.

Like I've described above, here below there's the original picture with the distortion correction applied:
![alt text][image7]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color (selection from HLS, LAB and RGB color space) and gradient thresholds to generate a binary image (thresholding steps available in the cell under the title **Color and gradient step**).  After setup the code stub, I've tried several parameters leading me to the results below. Unfortunately those parameters don't perform well on the additional challange. Here's an example of my output for this step.

![alt text][image8]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in the cell under the title **Find suitable perspective transformation**.  The function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  

![alt text][image9]
![alt text][image10]
![alt text][image11]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

In the 2 cells under the title **Find lanes** there's the code to extract and find the lanes. This code is for the most inspired by the code seen in the lessons: There's the algo for searching lines from scratch `fit_polinomial` and the one to use when we can rely on the previous detection `search_around_poly`


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Lane radius curvature has been calculated by leveraging on the class suggested in *Tips and tricks for the project*. I've implemented 2 classes: `Line` which calculates all the useful params associated to the line like `radius_of_curvature` or `line_base_pos` which stores the distance of the lane and a `history` of detection that are used to make the upcoming detection more stable. Then I implemented a class calle `Lane` which feeds the two `Line` classes with proper values coming from each frame in the method `process_image`. The most important methods are the `update` for both `Lane` and `Line` classes. In particular, in that method you can find the code to calculate the radius of curvature (which in the end equals to the average of the radius found on each line)

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Under the cell called **Test pipeline  some images** you can find the code that applies all the steps described above to some images. **Please pay attention** that, since the curvature and center distance is calculated on the classes described above, here I've tested only the other pipeline steps while I put fake data just to see if I was placing the text properly in the image. In the video instead you can find the real values of the curvature

![alt text][image12]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

As usual, finding correct parameters is always where I spend the most of the project time. Pipeline does work well, the classes implemented to buffer all useful data does a great job. I've tried to run the pipeline on the additional challanges but it fails. I assume that the parameters can be set better and maybe some more improvements on the  `Line` or `Lane` classes can increrase the presicion in detection. If I wanted to make it more stable I would switch to use segmentaiton nets since they seem to perform very better than the detection implemented. One question I have is: are those techniques that I used in this notebook still valid? Shouldn't we have to pick as a first choice the segmentation networks? Or maybe a combiination of both? I think I will investigate soon some of these technique a side of this project, but I really enjoyed lerning all these fundamentals
