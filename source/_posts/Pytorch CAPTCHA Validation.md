---
title: Pytorch CAPTCHA Validation
date: 2021-08-1 15:15:21
tags: ['Python','CV','Pytorch']
categories: 编程 Programming
---
# Purpose
This project is used as of a module to pass the CAPTCHA validations so that the crawler can download the desired documents. 
<!-- more -->
# Ideas 
- Get the images from the CAPTCHA requests and do some preprocessing to get each of the letter/number; these data are later used as the dataset.
- Use the CNN model to do the classification.
- Generalize the process so that the parameters for each website can be saved in database or local files.
# Issues & Solutions
- Pytorch-related
    - When image is stored as a np.array, the shape is in the format of (h,w,c) and, when the **to.tensor** (one of the transformation) in is applied, the image is automatically converted to the shape of (c,h,w).
    - When using the checkpoint to get the result, the specifications of the input data(e.g. **shape, type) have to be the same** as the what it is during the training process. 
        - It is better to use the torch.transformation directly with the np.adarray.
- CV-related
    - Use the contours to slice images.
        - **object to be found should be white and background should be black.**
        - Each individual contour is a Numpy array of (x,y) coordinates of boundary points of the object.
        - **Paddings** are very useful when some of the letters or digits are too big
        - **Erosion** is useful to remove noises
- Overall
    - The parameters of preprocessing would be different for each website so it is necessary to inject the parameter for each case.
        - First keep the parameter in some json files.
        - Keep the input shape of the images the same through out the processes.

# Unsolved
- When the CAPTCHA letters are connected in a way that cannot be simply sliced, more advanced segmentation techniques are required. There does not exist a very universal and general way to do this.
    - Potential solution is to apply ML to train a model that cut the letters correctly. But more works have to done.





