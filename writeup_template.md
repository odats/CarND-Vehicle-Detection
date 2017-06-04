**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image21]: ./examples/hog1.png
[image22]: ./examples/hog2.png
[image3]: ./examples/sliding_windows.jpg
[image41]: ./output_images/res1.png
[image42]: ./output_images/res2.png
[image43]: ./output_images/res3.png
[image44]: ./output_images/res4.png
[image5]: ./examples/bboxes_and_heat.png
[image51]: ./output_images/heat1.png
[image52]: ./output_images/heat2.png
[image53]: ./output_images/heat3.png
[image54]: ./output_images/heat4.png
[image55]: ./output_images/heat5.png
[image56]: ./output_images/heat6.png
[image57]: ./output_images/label.png
[image58]: ./output_images/heatres.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

**Histogram of Oriented Gradients (HOG)

The code for this step is contained in the [3] code cell of the IPython notebook.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image21]

![alt text][image22]

2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters and then compared errors after classification. To be honest I do not like this approach. I tried to find a magic numbers. For any classification task I will need to find new magic numbers. If I increase for example pix_per_cell than I loose some important details.

3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using LinearSVC. Test Accuracy of SVC is 96% My test_size is 0.2. I also shuffled data before training. The code for this step is contained in the [19] code cell of the IPython notebook.

***Sliding Window Search

I decided to go with scale = [1,3]. I checked the size of cars depending on how far away the car is from the camera. Having more scales affects the performance. Instead of overlap I defined how many cells to step (2), cell has 8 pixels, 8 cells per block.
The code for sliding window is contained in the [12] and [18] code cell of the IPython notebook.


To optimize the performance I decided to go with 2 scales and take HOG features for all image instead of taking HOG features for each window independently. My pipeline analysis only the bottom part of the image.

Ultimately I searched on two scales [1,3] using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image41]

![alt text][image42]

![alt text][image43]

![alt text][image44]
---

### Video Implementation

Here's a [link to my video result](./project_video.mp4)

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected. 
The code for heatmaps is contained in the [65] and [11] code cell of the IPython notebook. I used 2pixes for thresholding and 6 frames to get heatmaps.

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:


### Here are six frames and their corresponding heatmaps:

![alt text][image51]

![alt text][image52]

![alt text][image53]

![alt text][image54]

![alt text][image55]

![alt text][image56]


### Here is the output of `scipy.ndimage.measurements.label()` on the integrated heatmap from all six frames:

2 cars found

![alt text][image57]

### Here the resulting bounding boxes are drawn onto the last frame in the series:
![alt text][image58]



---

###Discussion

My pipeline will not work in case of different weather conditions (rain, winter) and different objects (for example bikes). Noise reduction and more training data can improve pipeline.

Another issue is that it takes a lot of time to process each frame. I understand that we can skip some frames and aggregate data. Another problem is that taking addvantage of different size of windows (sliding windows algorithm) dramatically decreases the performance of pipeline.

