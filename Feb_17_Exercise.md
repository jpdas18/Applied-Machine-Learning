#### Last time you did an exercise (convolutions and pooling) where you manually applied a 3x3 array as a filter to an image of two people ascending an outdoor staircase. Modify the existing filter and if needed the associated weight in order to apply your new filters to the image 3 times. Plot each result, upload them to your response, and describe how each filter transformed the existing image as it convolved through the original array and reduced the object size. 

![Plot](image_1_feb21.png)

#### The first image was created by applying the existing filter. filter = [ [-1, -2, -1], [0, 0, 0], [1, 2, 1]] This filter did a good job of emphasizing vertical lines. The next three filter will be my own; by playing around with the filters, I will get a better idea of how I can manipulate an image for computer vision.

![Plot](image_2_feb21.png)

#### This image was created by applying the filter = [ [0, 1, 0], [1, -4, 1], [0, 1, 0]]. It appears to make the image much darker. I wonder if that is because the number applied to the current pixel is -4. I am curious to see what happens if I increase or decrease the number in that position.

#### What are you functionally accomplishing as you apply the filter to your original array (see the following snippet for reference)? Why is the application of a convolving filter to an image useful for computer vision? 

#### Another useful method is pooling. Apply a 2x2 filter to one of your convolved images, and plot the result. In effect what have you accomplished by applying this filter? Does there seem to be a logic (i.e. maximizing, averaging or minimizing values?) associated with the pooling filter provided in the example exercise (convolutions & pooling)? Did the resulting image increase in size or decrease? Why would this method be useful? Stretch goal: again, instead of using misc.ascent(), apply the pooling filter to one of your transformed images.

#### Convolve the 3x3 filter over the 9x9 matrix and provide the resulting matrix. 

#### Stretch goal: instead of using the misc.ascent() image from scipy, can you apply three filters and weights to your own selected image? Again describe the results.
