## Writeup Template
### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector.
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/1_vehicle_image_samples.png
[image2]: ./output_images/2_non_vehicle_image_samples.png
[image3]: ./output_images/3_hog_feature.png
[image4]: ./output_images/4_test_image_histogram.png
[image5]: ./output_images/5_test_image_spatial.png
[image6]: ./output_images/6_test_image_hog.png
[image7]: ./output_images/7_sliding_window.png
[image8]: ./output_images/8_search_window.png
[image9]: ./output_images/9_test_image_heatmap.png
[image10]: ./output_images/10_pipeline_image.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in code_cell_12, code_cell_13 and code_cell_14.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here are some examples of the `vehicle` and `non-vehicle` images:

![alt text][image1]
![alt text][image2]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![alt text][image3]

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters for orientation, pix_per_cell and cells_per_cell until I reached to the final combination that allowed me to detect vehicles with high accuracy.
I have also applied a color transform and spatial binning features, as well as histograms of color, to me HOG feature vector which helped me to increase the number of feature and hence helped me in training my model.
Here are my final numbers for all the feature extraction:

		Color_Space = 'YUV'
		Spatial_Size = (16,16)
		Histo_Bins = 32
		Histo_Bins_Range = (0,256)
		Orientation = 11
		Pixels_Per_Cell = 16
		Cells_Per_Block = 2
		Hog_Channel = "ALL"

Here are some examples on the used techniques on test images:

**Color Histogram:**
![alt text][image4]

**Spatial Binning:**
![alt text][image5]

**Hog feature:**
![alt text][image6]

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using LinearSVC(C=0.01), I have used the previous combinations methods to extract my features.
I have normalize the features and randomize a selection for training with 80% of data and testing with 20% of data.

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to define an area of search where the vehicle would appear and then I applied multi scale windows with different sizes and overlapping.

I have used the following windows with the following parameters for each:

Window0:
			x_start_stop = [0, 1300]
			y_start_stop = [400, 680]
			window0_size = (64, 64)
			overlap0 = (0.85, 0.85)

Window1:
			x_start_stop = [0, 1300]
			y_start_stop = [400, 680]
			window1_size = (96, 96)
			overlap1 = (0.75, 0.75)

Window2
			x_start_stop = [0, 1300]
			y_start_stop = [400, 680]
			window2_size = (128, 128)
			overlap2 = (0.65, 0.65)

Here's an example of the windows scale:
![alt text][image7]

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I searched on two scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image8]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./project_video_output.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of **scipy.ndimage.measurements.label()** and the bounding boxes then overlaid on the images:
![alt text][image9]

Here's also the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image10]

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The algorithm may fail in some cases of difficult light conditions, which could be partly resolved by the classifier improvement.
The algorithm may have some problems in case of one car overlaps another. We can use long-term memory of car position to predict where the car will be in the next time.
Perhaps we will try deep learning algorithm to detect much more kinds of objects in other project. Deep learing algorithm may also be used in detecting cars.
