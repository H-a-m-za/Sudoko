# Smart Sudoku Solver
This Project aims to solve a sudoku grid by detecting it using classical computer vision algorithms, then Detecting the numbers in each cell using OCR, then finally solving the sudoku
---
## Phase 1

Description: Phase 1 aims to get the image of the board and then extract each individual suduko tile

Inputs: Sudoku Image

Outputs: 81 tiles of sudoku

Process of solution:

There were 5 steps in order to tackle this problem:

### **1) Preprocessing :**

1.1) Convert Image to gray Scale

1.2) If image is too dark (The maximum pixel intensity in the image is < 100), then apply gamma correction using the following formula:
                                
                                \[I(new) = \left( \frac{I}{255} \right)^{\gamma} \times 255\]
  
We used gamma to be = 15

1.3) Apply Median Filter with kernel sie 5x5 7 times. 
     We chose this as our noise removal technique. 
     We also tried gaussian filter and minimum filter but they were not effective

1.4) After this we detect if image is dark mode or not
     i.e.: The most repeated intensity in the image now is < 127 
           and the maximum intensity in the image is > 240 
           (the second condition is to ensure that image is not just a dark image)
     If both conditions are satisfied then image is inverted

1.5) Now comes the thresholding step:

1.5.1) We tried global thresholding which has proven to be a failure in images    with bright and dark areas

1.5.2) We chose Adaptive Thresholding with the following parameters:

       Method: Gaussian C
       
       blockSize: 111
       
       C: 5
       

1.6) Then, we applied dilation with a kernel 3x3 to thicken the lines which helps in detecting curved lines


### **2) Edge Detection**

2.1) We applied Hough Transform with following parameters:

     minLineLength : minimum_image_dimension / 4
     
     rho: 1

     theta: pi/180
     
     threshold: 200
     
     maxLineGap: 45
     
  But after we applied Hough to get the lines we found that some lines get neglected, also lines are too thick which can result in some background features to get recognized as sudoku frame

2.2) So, we applied canny, to thin the lines and reduce the effect of the  background on the detection, also to reduce the number of intersection points in the next stage 

2.3) When we applied hough again, we found that it didn't detect the lines because they were too thin so we did the following:
      Dilation with kernel size 5x5 twice
      Erosion with kernel size 3x3 once

2.4) Now apply Hough again to get the lines with following parameters:

     minLineLength : minimum_image_dimension / 3
     
     rho: 1
     
     theta: pi/180
     
     threshold: 200
     
     maxLineGap: 50
     

### **3) Getting Intersection points**

3.1) Here We loop over the lines and get the angle between each two lines, we only accept intersections that are between 80 to 100 degrees. Also, to further remove the effect of the background we only accept intersections on a line if that line has been intersected eight times or more (what characterizes the sudoku lines is that they are intersected a lot)

### **4) Getting Outermost intersection Points**

4.1) Now after the reducing the background effect, we can now say that the outermost intersection points are the sudoku frame points

### **5) Warp the image**

5.1) We warp the image using the outermost intersection points and divide the image to tiles



