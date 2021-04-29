# SFND 2D Feature Tracking

<img src="images/keypoints.png" width="820" height="248" />

The idea of the camera course is to build a collision detection system - that's the overall goal for the Final Project. As a preparation for this, you will now build the feature tracking part and test various detector / descriptor combinations to see which ones perform best. This mid-term project consists of four parts:

* First, you will focus on loading images, setting up data structures and putting everything into a ring buffer to optimize memory load. 
* Then, you will integrate several keypoint detectors such as HARRIS, FAST, BRISK and SIFT and compare them with regard to number of keypoints and speed. 
* In the next part, you will then focus on descriptor extraction and matching using brute force and also the FLANN approach we discussed in the previous lesson. 
* In the last part, once the code framework is complete, you will test the various algorithms in different combinations and compare them with regard to some performance measures. 

See the classroom instruction and code comments for more details on each of these parts. Once you are finished with this project, the keypoint matching part will be set up and you can proceed to the next lesson, where the focus is on integrating Lidar points and on object detection using deep-learning. 

## Dependencies for Running Locally
* cmake >= 2.8
  * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1 (Linux, Mac), 3.81 (Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* OpenCV >= 4.1
  * This must be compiled from source using the `-D OPENCV_ENABLE_NONFREE=ON` cmake flag for testing the SIFT and SURF detectors.
  * The OpenCV 4.1.0 source code can be found [here](https://github.com/opencv/opencv/tree/4.1.0)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools](https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

## Basic Build Instructions

1. Clone this repo.
2. Make a build directory in the top level directory: `mkdir build && cd build`
3. Compile: `cmake .. && make`
4. Run it: `./2D_feature_tracking`.

## Writeup

* The various steps followed are as follows: 
  * MP.1: Use a vector and keep pushing new images to the vector. However once the size of the vector is greated than  hte buffer size delete the oldest image(in this case the first element of the vector) and continue pushing new images to this vector.
  * MP.2: Using open CV and based on input string type, modified code to select appropiate keypoints.
    These keypoint extractors also known as feature detectors, use the information related to intensity and respective gradients to obtain desired keypoints, such that they are easy to loacte in subsequent images.
  * MP.3: Using rect method in OpenCV, we can focus only a specific region of interest containing the preceding vehicle. This is later useful in obtaining TTC.
  * MP.4: Using OpenCv implemented BRIEF, ORB, FREAK, AKAZE and SIFT descriptors. Descriptors are clasified as HOG based on binaray descriptors. Later the performance of these descriptors are evaluated w.r.t computation time. 
  * MP.5: Matching decriptors based on L1 norm is known as Brute force method is implemented using OpenCV. Furhter, KD tree based algorithm known as FLANN us also implemented to reduce the computational time.
  * Mp.6: to further improve the performance of FLANN by reducing the number of False positives, a technique called descriptor distance ratio is implemented.
 
  * MP.7 Performance Evaluation 1
    Count the number of keypoints on the preceding vehicle for all 10 images and take note of the distribution of their neighborhood size. Do this for all the detectors you have implemented.

    | Detector    | image0 | image1 | image2 | image3 |image4 | image5 |image6 | image7 | image8 | image9 | Neighborhood size
     | :---       |:----: |:----: |:----: |:----: |:----: |:----: | :----: |:----: |:----: |:----: |:----: |      
    | SHI-TOMASI  | 125 | 118 | 123 | 120 | 120 | 113 | 114 | 123 | 111 | 112 | 4
    | HARRIS      | 17	| 14  | 18	| 21  | 26	| 43  |	18	| 30  |	26	| 34  | 6
    | FAST        | 149 | 152 | 150 | 155 | 149 | 149 | 156 | 150 | 138 | 143 | 7
    | BRISK       | 264 | 282 | 282 | 277 | 297 |279  |	289 | 272 | 266 | 254 | 11
    | ORB         |  92 | 102 | 106 | 113 | 109 |125  | 130 | 129 | 127 | 128 | 31
    | AZAKE       | 166	| 157 |	161	| 155 |	163	| 164 |	173	| 175 | 177	| 179 | 4.8
    | SIFT        | 138	| 132 |	124	| 137 |	134	| 140 |	137	| 148 |	159	| 137 |5.6

   * MP.7 Performance Evaluation 2
      Count the number of matched keypoints for all 10 images using all possible combinations of detectors and descriptors. In the matching step, the BF approach is used with the descriptor distance ratio set to 0.8. Sum of the key point matches in 10 pairs of images are included.

      Some combinations of detector and descriptor doesn't make sense, those results are N/A.

      | Detector/Descriptor    | BRISK | BRIEF | ORB | FREAK |AKAZE | SIFT |
      | :---        		   |:----: |:----: |:----: |:----: |:----: |:----: |     
      | SHI-TOMASI             | 767 | 934 | 906 | 761 | NA |926 |
      | HARRIS                 | 142 | 173 | 162 | 144 | NA |163 | 
      | FAST                   | 2182| 2831| 2768| 1982| NA |947 | 
      | BRISK                  | 1570| 1704| 1514| 1524| NA |1646| 
      | ORB                    | 751 | 545 | 763 | 420 | NA |760 | 
      | AZAKE                  | 1215| 1125| 1182| 1187|1172|1270| 
      | SIFT                   | 520 | 702 | NA  | 506 | NA |800 | 


	* MP.7 Performance Evaluation 3
      Log the time it takes for keypoint detection and descriptor extraction in milli seconds. The TOP3 detector / descriptor combinations must be recommended as the best choice for our purpose of detecting keypoints on vehicles.

      Some combinations of detector and descriptor doesn't make sense, those results are N/A.
      | Detector/Descriptor    | BRISK | BRIEF | ORB | FREAK |AKAZE | SIFT |
      | :---        		   |:----: |:----: |:----: |:----: |:----: |:----: |          
      | SHI-TOMASI             | 20.43 | 19.84 | 19.64 | 55.41 |NA | 31.48 | 
      | HARRIS                 | 19.35 | 21.32 | 21.67 | 56.38 |NA | 46.78 | 
      | FAST                   |   7.1 |  4.18 | 3.96	| 53.67 | NA | 38.56 | 
      | BRISK                  | 417.23 | 416.89 | 419.74	| 468.38 | NA |458.29 | 
      | ORB                    | 10.14  | 10.08  | 16.56  | 54.18  | NA | 86.45 | 
      | AZAKE                  | 113.79 | 114.23 | 114.56 |156.26  | 196.38 |137.91 | 
      | SIFT                   | 167.13 | 166.36 |NA | 210.57 | NA |241.93 | 


      The top 3 detector/descriptor combinations are found by evaluating the tables above.

      In terms of number of matched keypoints (More is better)

      FAST/BRIEF
      FAST/ORB
      FAST/BRISK
      In terms of execution time (Small is better)

      FAST/ORB
      FAST/BRIEF
      FAST/BRISK
