---
layout: post
title: '[Computer Vision] Bundle Adjustment PT1'
category: slam
description: >
  Bundle adjustment
---

Source [Lecture by Cyrill Stachniss](https://www.youtube.com/watch?v=sobyKHwgB0Y)

3D reconstruction with multiple images

- Feature extraction at each image
- Least squares approach to estimating camera poses and 3D points
  - Start with an initial guess
  - Project the estimated 3D points into the estimated camera images
  - Compare locations of the projected 3D points with measured (2D) ones
  - Adjust to minimize error in the images

Pixel coordinate
$$
^a\mathbf{x}_{ij}'+^a\hat{\mathbf{v}}_{x_{ij}'}=\hat{\lambda}_{ij} ^a\hat{P}_j(\mathbf{x}_{ij}, \mathbf{p}, \mathbf{q})\hat{\mathbf{X}}_i
$$

with

$$
\Sigma_{x_{ij}, x_{ij}}, i=1, \dots, I_j; j=1, \dots, J
$$

- $$^a$$ : arbitrary frame
- $$_{ij}$$ : point $$i$$ observed in image $$j$$
- $$\hat{\mathbf{v}}$$ : corrections
- $$\hat{\mathbf{X}}_i$$ : Estimated 3D location of the point $$i$$ (guess) in homogeneous coordinate
- $$^a\hat{P}_j$$ : Projection matrix
    - $$\mathbf{x}_{ij}$$ : image pixel coordinate
    - $$\mathbf{p}$$ : projection parameters : intrinsic parameters (typically 5)
    - $$\mathbf{q}$$ : distortion
- $$\hat{\lambda}$$ : Scale factor
- $$\Sigma_{x_{ij}}$$ : uncertainty in the image coordinates
  - $$I_j$$ : number of points in image j
  - $$J$$ : number of images