---
title: Generative Adversarial Network
date: 2021-08-13 14:17:55
tags: ['Python','ML']
categories: 编程 Programming
---
# Training
- Generator **G**
    - Create some high dimensional object that can fool the discriminator
- Discriminator **D**
    - Criticize the the outputs form the Generator
- Data distribution **p<sub>data</sub>**
- Noise distribution **p<sub>z</sub>**
    - Relatively low dimensional space 
<a href="https://ibb.co/GWg1Jmq"><img src="https://i.ibb.co/T4znTys/1281628836217-pic-hd.png" alt="1281628836217-pic-hd" border="0"></a>

## Generator step: 
    - Keep the weights in the discriminator 
    - Update **G** based on the loss produced by **D**
## Discriminator step: 
    - **D** inputs a sample from **G** based on **p<sub>z</sub>** and we want the loss to be high
    - Keep the weights in the **G**
    - **D** inputs a sample from real world example to make sure the **D** can still distinguish the real ones and fakes

# Applications and Examples
## Superresolution
<a href="https://ibb.co/9hK8rKd"><img src="https://i.ibb.co/r387w8X/1291628837087-pic-hd.png" alt="1291628837087-pic-hd" border="0"></a>
- PSNR (the metric on the left)
    - Peak signal to noise ratio
- SSIM (the metric on the right)
    - An image quality reference (High number if the sample image match with the reference image in a "perceptual sense").
- The GAN produced image has sharper details but those details are **completely different** from the original ones even they look reasonable.
    - Pros
        - Creating plausible results
        - Images with high qualities
    - Cons 
        - Not accurate
- Relative work
    - ESRGAN(Extended superresolution GAN)

## Image to Image Translation(aka pixel to pixel) (Isola et al, 2016)
<a href="https://ibb.co/mGRY0F5"><img src="https://i.ibb.co/L80qrhd/1301628837959-pic-hd.png" alt="1301628837959-pic-hd" border="0"></a>
- Use **paired images to train** the model and the outputs are completely different from the ground truth.
## Unparied image Translation (Zhu et al, 2017)
<a href="https://ibb.co/x2wr847"><img src="https://i.ibb.co/BfvYK1C/1321628838935-pic-hd.png" alt="1321628838935-pic-hd" border="0"></a>
- In the first pair of images, the zebras are replaced by the horses and the other parts of the image remain the same. This is not possible to find paired images to train and achieve such goals.
- CycleGAN
    - Have a collection of zebras and a collection of horses; then train the model to be able to map horse pictures to zebra pictures and vise versa
    - Include two sets of GANS
        1. Mapping from zebras to horse
        2. Mapping from horse to zebras
    - Apply the **cycle consistency loss**
        - zebra image -> horse image -> zebra image
        - The two zebra images are close in pixelwise sense

# Metrics       
## Inception Score    
$$
\operatorname{IS}(G)=\exp \left(E_{x \sim p a} D_{K L}(p(y \mid x) \| p(y))\right)
$$
- It is for general use cases unlike SSIM.
- Backbone of Inception Score is a pre-trined classifier called the **Inception Network**.
- **p(y|x)** is the Inception's likelihood that image **x** is class **y**. Expectation is taken over data distribution, which is estimated by a large minibatch.
- **D<sub>KL</sub>** is the KL divergence that captures the differences between different distributions.
<a href="https://ibb.co/CMH8HDC"><img src="https://i.ibb.co/NmyCybD/1331628840072-pic-hd.png" alt="1331628840072-pic-hd" border="0"></a>
- We want individual scores to be high when the samples have the same label and similar scores when the samples are different.
- **Trade-off between the individual image quality and diversity**

## Frechet Inception Distance
$$
d^{2}\left((\boldsymbol{m}, \boldsymbol{C}),\left(\boldsymbol{m}_{w}, \boldsymbol{C}_{w}\right)\right)=\left\|\boldsymbol{m}-\boldsymbol{m}_{w}\right\|_{2}^{2}+\mathbf{T r}\left(\boldsymbol{C}+\boldsymbol{C}_{w}-2\left(\boldsymbol{C} \boldsymbol{C}_{w}\right)^{1 / 2}\right)
$$
- FID compares the distribution of generated images with the distribution of real images that were used to train the generator.
- **m** is a vector of mean Inception activations
- **C** is a covariance matrix of Inception activations
- **Tr(.)** is the trace of the matrix 
# Self-attention GAN (Han Zhang, Lan Goodfellow, Dimitris Metaxas, Augustus Odena)
- Huge improvements of the performance
<a href="https://ibb.co/1THcvjH"><img src="https://i.ibb.co/89FkKtF/1341628841128-pic-hd.png" alt="1341628841128-pic-hd" border="0"></a>
# Similar work
- VAE
- Autoregressive