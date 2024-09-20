# -CloudPhysician-THE-VITAL-EXTRACTION-CHALLENGE

This is a project made as a solution of - The Vital Extraction Challenge (By CloudPhysician). 
- The core problem statement was to extract Heart Rate, SpO2, RR, Systolic Blood Pressure, Diabolic Blood Pressure, and MAP from the provided images of Modular patient monitor.

# Introduction
This collab notebook takes in the paths of images of different patient monitors, and outputs the values shown on the monitor, which are HR, SPO2, RR, SBP, DBP and MAP respectively. This notebook is made as a solution of the inter-IIT 2023 Problem Statement 6 - The Vital Extraction Challenge (By CloudPhysician)

#How to run
For the first time run, run the first block of 'Installing Necessities, Importing essential Libraries and models - '. It will load all the necessities into the memory, to be read for the inference function.
This block needs to be run only once, and it will take 20-30 seconds to run.

Also run all the blocks below the first block inorder to define the necessary functions.

**Note** - The SegmentationModel.h5 file present in the zip file should also be present in the Collab environment inorder for the notebook to run.

To run the functions, Run the inference function in the Final Function block. 

# Pipeline
The problem can be divided into 2 subtasks - 


*   Finding the monitor in the image and cropping it.
*   Finding all the details on the monitor and finding which detail is which and generating the output accordingly.

For finding the monitor coordinates, I am using an deep CNN network, with an EfficientNetB4 backbone (pretrained imagenet weights). The model took in resized images from (1280,720) pixels to (380,380) pixels dimensions. I augmented the 2000 images of the segmentation dataset given to us, and created a dataset of 8000 images, and trained the model onto it. The rms error after training for 50 epochs, was 6.87, and validation rms error was 8.67.

The model is built, trained, and deployed via the keras API of tensorflow.

Also, the model is run on padded images (600 width, 338 height), inorder to give better accuracy on images which are really close to the edge of the image.

<!-- <img src = https://cdn.discordapp.com/attachments/1072573248493076563/1072573373495918632/output.png> -->

For finding all the details on the cropped Image, PaddleOCR is used. It detects all the text and the numbers present in the image, and stores it in a numpy array. It stores the word/number detected, the bounding box of the number detected and the confidence level of it.

<img>

After this, we use an algorithmic approach to find out all the relevant numbers - 


*   Firstly, I delete all the detections which are smaller than a minimum threshold value, inorder to eliminate noise from the detections.
*   Then we detect the HR in the image. We find any keywords equal or related to the word 'HR' (that might be present on the monitor) and print the closest integer to it. If no such word is found, we look at the numbers which are colored green, and print them out.If still not found we compare the respective values and take the topmost value finally.
*   By finding out the word SpO2 or similiar words and finding the numbers closest to it we get SpO2.
*   Similarly we find out the RR by looking for keyword 'rr','resp' and if not found we look for numbers under 45 and take them as RR. 
*   Then, I am using 4 different methods to detect BP and MAP in the image - 
    *   We first try to detect the BP by finding either '/' ad then if we get the value I am taking the first part as SBP and after '/' part as DBP.
    *   Our second attempt to find the BP is by looking for keywords like 'sys', '5y5', '5u5', 'dia', 'mmhg', 'winhg','nibp', 'nbp'. and then finding the nearest bounding boxes to it.
    *   We find Map by looking for '(' or ')'  and then if we got it we will take the numbers after '(' or before')'.If not found then we look for the keyword 'map' and find the bounding box nearest to it
    *   The last attempt to find out the BP and MAP is by looking for three numbers having closest distance and within distances  within a threshold and then trying to find the SBP,DBP and MAPs
*   After all that, I am simply arranging it all in a dictionary and outputting it.  
  




# Libraries used - 


*   Tensorflow (for running keras models)
*   OpenCV (for image processing)
*   PaddleOCR (for OCR functionality)
*   Numpy (for array calculations)
*   ColorThief (for finding dominant colors)
*   Matplotlib (for plotting HR graphs)
*   Deskew (for deskewing the original images)
*   NeuroKit2 (for displaying HR graph from Heart rate)
