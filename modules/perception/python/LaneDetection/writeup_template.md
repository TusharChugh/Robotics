# **Finding Lane Lines on the Road**

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

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

My pipeline consisted of 5 steps. First, I converted the images to gray scale, then I blurred the image using gaussian kernel. Next, the canny edge detection is used to detect the edges in the image. We can clearly see there are a lot of extra edges in the image especially on the dividor between the road and the ground. One way to solve this would have been using the information in colored image to remove the green part but these kind of dividors are not also green and occasionally can be of yellow or white colors as well.
To remove unwanted edges from the images, region of interest is selected which spans the lower half of the image from where the road is visible. Note that the values I have in code will work if the camera is mounted at similar location. It won't work on side camera's.
Hough transform is used to get few lines from the image. Based on the parameters I selected, 5-20 lines can be expected from the image.

The major part started after getting the lines from hough transform.

In order to draw a single line on the left and right lanes, I didn't modified the draw_lines() function, instead I added a couple of more helper functions. First, multiplelines_to_twolines takes the line data from hough transform and depending on the slope (+ve or -ve), it puts in left or right lane bucket. Initially, I was just taking the average of all the slopes and intercept to get the final slope for the respective lines. It wasn't giving good results, but then I changed this to weighted average with weights being the squared length of the line. The idea is similar to least squares, here larger lines get much more weightage than the smaller line. After getting slope and intercept from this function it was fairly straight forward to get the values in pixel. I selected the points which are at the bottom of the image and which are at 60% of the image height (70% doesn't work well with curvy roads and 55% seems too short to visualize the results properly)

If you'd like to include images to show how the pipeline works, here is how to include an image:

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the roads have curvatures. Hough Line transform doesn't work well in that case. In addition, the code assumes that we have straight line when getting the final lane lines.

Another shortcoming could be caused by if the lines are faintly visible especially the dotted ones. The resulting lines can jump all of a sudden to a totally different location.

The parameters are also trained after watching the results on the images. So, potentially there is an over-fitting while tuning these params. It might not work well on some new dataset.

Lighting conditions and severe weather conditions like rain, snow , heat(mirage) can affect the reliability.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to use hough generalized transforms (for curved roads). Second, would be to increase our dataset and use cross validation to make sure that we are not over-fitting.

We can solve the jumping problem by comparing our new parameters for lines with the median of our previous values. If there is too much of an aberration we can use median or the last values rather than using this erroneous value. 

Another potential improvement could be to deep learning models.
