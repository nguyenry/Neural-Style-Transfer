<br>
<img src="https://github.com/nazianafis/Resources/blob/main/NST/NST-gif.gif" alt="header" align="right" width="270"/>

# Colour-Preserving Neural Style Transfer (NST)

Neural Style Transfer is the ability to create a new image (known as a pastiche) based on two input images: one representing the content and the other representing the artistic style.

This repository contains a lightweight PyTorch implementation of art style transfer discussed in the seminal paper by [Gatys et al.](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf) To make the model faster and more accurate, a pre-trained VGG19 model is used.

What our project does differently from [Gatys et al.](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf) is preserve the original colors from the content image.

## Table of Contents

1. [Overview](#overview)
2. [How does it work?](#working)
3. [File Description](#description)
4. [Getting Started](#getting-started)
    1. [Dependencies](#dependencies)
    2. [Usage](#usage)
5. [Output](#output)
6. [Acknowledgements](#cite)

## Overview <a name="overview"></a>

Neural style transfer is a technique that is used to take two images—a content image and a style reference image—and blend them together so that output image looks like the content image, but “painted” in the style of the style reference image.

## How does it work?<a name="working"></a>

1. We take content and style images as input and pre-process them.
    1. For the style image, we do another step of pre-processing to preserve the color of the content image.
    2. From an RGB image, convert to YUV color space.
    3. Resize the image to be the same size as the content image.
    4. Convert the content image to YUV and replace the U and V color channels from the style image with the U and V channels from the content image.
    5. Convert the new style image back to RGB to pass into VGG19.
2. Next, we load VGG19 which is a pre-trained CNN (convolutional neural network).
    1. Starting from the network's input layer, the first few layer activations represent low-level features like colors, and textures. As we step through the network, the final few layers represent higher-level features—like eyes.
    2. In this case, we use `conv1_1`, `conv2_1`, `conv3_1`, `conv4_1`, `conv5_1` for style representation, and `conv4_2` for content representation.    
![](https://github.com/nazianafis/Resources/blob/main/NST/NST-architecture.png)

3. We begin by cloning the content image and then iteratively changing its style. Then, we set our task as an optimization problem where we try to minimize:
    1. **content loss**, which is the L2 distance between the content and the generated image,
    2. **style loss**, which is the sum of L2 distances between the Gram matrices of the representations of the content image and the style image, extracted from different layers of VGG19.
    3. **total variation loss**, which is used for spatial continuity between the pixels of the generated image, thereby denoising it and giving it visual coherence.
4. Finally, we set our gradients and optimize using the L-BFGS algorithm to get the desired output.

## Getting Started <a name="getting-started"></a>

### File Description <a name="description"></a>

    Neural-Style-Transfer
        ├── data
        |   ├── content-images
        |   ├── style-images
        ├── models/definitions     
        │   ├── vgg19.py   <-- VGG19 model definition
        ├── NST.py  <-- the main python file
        ├── LICENSE
        └── README.md

### Dependencies <a name="dependencies"></a>
*    Python 3.9+
*    Framework: PyTorch
*    Libraries: os, numpy, cv2, matplotlib, torchvision

### Usage <a name="usage"></a>

```
    $ pip install -r requirements.txt
```

#### To implement Neural Style Transfer on images of your own:

1. Clone the repository and move to the downloaded folder:
```
    $  git clone https://github.com/nazianafis/Neural-Style-Transfer
```
```
    $  cd Neural-Style-Transfer
```
2. Move your content/style image(s) to their respective folders inside the `data` folder.

3. Go to `NST.py`, and in it, set the `PATH` variable to your downloaded folder. Also set `CONTENT_IMAGE`, `STYLE_IMAGE` variables as your desired images:
```
    $ PATH = <your_path>
   
    $ CONTENT_IMAGE = <your_content_image_name>
    $ STYLE_IMAGE = <you_style_image_name>
```
4. Run `NST.py`:
```
    $ python NST.py
```
5. Find your generated image in the `output-images` folder inside `data`.

## Output <a name="output"></a>

The following images were generated using no image manipulation program(s) other than the code described in this article.

<img src="https://github.com/nazianafis/Resources/blob/main/NST/NST-outputs.png" alt="content" width="700"/>


## Citations <a name="cite"></a>

* Nazia Nafis's [implementation of conventional NST](https://github.com/nazianafis/Neural-Style-Transfer).
* Pavel Gonchar's [implementation of color-independent NST with TensorFlow](https://github.com/pavelgonchar/color-independent-style-transfer).
* PyTorch's [tutorial on NST](https://pytorch.org/tutorials/advanced/neural_style_tutorial.html).
* The original paper on neural style transfer by [Gatys et. al](https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/Gatys_Image_Style_Transfer_CVPR_2016_paper.pdf).
* The original paper on [VGG19](https://arxiv.org/abs/1409.1556).
* [Wikimedia](https://commons.wikimedia.org/wiki/Category:Images), [Unsplash](https://unsplash.com/) for all the content and style images.
