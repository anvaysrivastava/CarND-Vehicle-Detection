**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/download-4.png
[image2]: ./output_images/download-5.png
[image3]: ./output_images/download.png
[image4]: ./output_images/download-1.png
[image5]: ./output_images/download-2.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in 'Defining HOG function' section of the IPython notebook

I generated Hog features for each channel.

Here is the example for car image in RGB.

![alt text][image1]

I then created a basic classifier and evaulatate which Channel gave maximum accuracy. It turned out to be YCrCb.


I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`). 

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

I then created a basic classifier to evaulatate which Channel gave maximum accuracy. It turned out to be YCrCb.

I had kept the orientation same as in the teaching material. Changing them was not leading to significant increase in accuracy of classifier.

####3 . Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

You can see the section `Training the classifier` for code.

For training the classifier I used a LinearSVC.

For X value of Linear SVC I did the folling
* Convert image to yCrCb
* ravel the input image and append it to fetures.
* append histogram for each channel containing 16 bins each.
* append hog features for each channel.

Then for each channel I did a scalar tranform so that all the variable get equal weight.


### Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

Code is present in section `Implement sliding window`

I first created a crude sliding window where the window size was 100x100 and shifting was also by 100x100. To this I added manual offset to evaluate that my crude sliding window evaluates images correctly.

![alt text][image3]

Once this was showing promise, I made the sliding window more granular by making the shift at 50x50 and windows at 70x70 and 140x140. This was working fine for sample video.

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

My code was scanning for y height from 400 and below.

Also rather than shifting per block, I made stronger strides, which did not lead to a loss of output image as you can see.

![alt text][image4]

Then in order to remove false positives and out number false failed detection, I used a heat_map approach. and merged the heapmap using `scikit's label`

Here you can see the heatmap for same image. The threshold is `5`
![alt text][image4]

---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_out.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The problems I have with my implementation are
* I believe SVC classifier is too weak and is giving too many false positives. I would have prefered to go with a Neural network.
* There is no tracking between the frames of video for car. Every frame is evaluated on its own, because of which in the shadow area you can see a few frames falsely claiming a car. I would like to keep a tracker which would track relative position between frames so that sporadic false positives could be eliminated.
