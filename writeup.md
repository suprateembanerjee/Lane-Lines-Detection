# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/solidYellowCurve2.png "Broken"
[image2]: ./test_images_output/hough_transformed.png "Hough"
[image3]: ./test_images_output/final.png "Final"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

The pipeline consists of several stages as follows:

1. Extracting an image frame from the video

2. Grayscaling the image frame

    Converting from three channels to one channel helps us detect edges that may occur in any colour. By normalizing the spectrum from RGB to Black and White, it is easier to detect high frequency areas.

3. Blur the image frame

    Although Canny Edge detection does this step automatically by itself, additionally blurring the image before passing it to the algorithm has different and improved effects.

4. Extract edges using Canny Edge Detection Algorithm

    This algorithm finds out the high frequency areas in the image which are usually edges. We pass in two threshold values alongside the image. The lower threshold is used to eliminate any pixel with an intensity value below that number. The upper threshold is used to consider all pixels above it as being pure white, ie. 255. The pixels that fall in between the thresholds are analysed, and if they do form a high frequency together, they are made a part of the output image.The resultant image is similar to a chalkboard sketch, black background with white borders. I found the values 50 for lower threshold and 150 for upper threshold to work reasonably well.

5. Region Specification

    The region is selected such that most of the unwanted/unnecessary bits are excluded from consideration. For us, the lane lines are the primary concern, and so I have used a quadrilateral that encompasses the region where the lane lines are expected in the fixed camera footage. These values are defined in the variable named 'vertices'.

6. Drawing the lines from the Canny dots

    Hough Transform is used to transform the region-bounded Canny edges into lines. I have used Rho = 1, Theta = one degree, threshold = 5, minimum line length = 50 and maximum line gap = 35 as they seem to offer the best results. Reducing the max line gap tended to separate the lower broken lines before the drawing function was modified to draw a single line. The transformation function itself calls the function responsible for drawing the lines on, ie annotating the video.
    
![alt text][image1]

  How I modified the drawing lines function:
  
  * I created 4 numpy arrays, containing the X and Y values for the right and left lane lines. 
  * I iterated through the lines to determine whether the slope was negative or positive, and added the point to the respective arrays. 
  * I used Numpy Polyfit and Poly1D functions to draw and extrapolate the lines. 
  * To extrapolate the line, I used the maximum Y value, which is the base of the image, and estimated the X value using the slope of the line. 
  * This was repeated for the other lane line as well.
    
![alt text][image2]

7. Finishing touches

    I have finally used a weighting function to make the lines semi-transparent and more appealing to the eye. I have not changed from the red colour as shown in the video as I felt this best annotated the image.
![alt text][image3]



### 2. Identify potential shortcomings with your current pipeline


The project did work reasonably well on straight lane lines, but suffered when the lines curved at a distance. When this happened, the lines got shortened.

Furthermore, on the Challenge video, I found very erratic placement of lines which logically do not represent the lane lines in any manner.The lane lines were completely unstable. This unstability extends to the other videos as well, but to a certain considerable limit. For instance,there is a frame towards the end of the video titled 'SolidWhiteRight', which is almost horizontal, and thereby inexplicable. There are rare unstabilities observed on the 'SolidYellowLeft' video as well, albeit limited in extent.

Overall, in the road area right ahead of the car, the lane lines generated seem to depict reasonably well where the car should go. The code creates complications as we proceed to look at the further end of line of sight, where sometimes the lane line annotations intercept each other, and occationally flicker in an unstable manner.


### 3. Suggest possible improvements to your pipeline

Stability remains an open problem to be solved in this code. The lines are not as stable as would be desired in a real world self driving car.

Curves need to be addressed, and instead of drawing a line, a traced curved annotation would be more robust and better represent a real world.
