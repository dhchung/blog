---
layout: post
title: '[Computer Vision] Bundle Adjustment PT1'
category: computer_vision
description: >
  Bundle Adjustment
# comments: true
---

Source [Lecture by Cyrill Stachniss](https://www.youtube.com/watch?v=sobyKHwgB0Y)

Bundle adjustment is an optimization process which minimizes the [Reprojection Error](#reprojection-error) to get the [Unknowns](#unknonwns) by solving the [Normal Equations](#setting-up-and-solving-the-system-of-normal-equations).



3D reconstruction with multiple images

- Feature extraction at each image
- Least squares approach to estimating camera poses and 3D points
  - Start with an initial guess
  - Project the estimated 3D points into the estimated camera images
  - Compare locations of the projected 3D points with measured (2D) ones
  - Adjust to minimize error in the images

## Reprojection Error

$$
^a\mathbf{x}_{ij}'+^a\hat{\mathbf{v}}_{x_{ij}'}=\hat{\lambda}_{ij} ^a\hat{P}_j(x_{ij}, \mathbf{p}, \mathbf{q})\hat{\mathbf{X}}_i
$$

- Left hand side : Point's actual coordinates in the image coordinate system + error
- Right hand side : Projection of estimated 3D point in the image coordinate system

with

$$
\Sigma_{x_{ij}, x_{ij}}, i=1, \dots, I_j; j=1, \dots, J
$$

- $$^a$$ : arbitrary frame
- $$_{ij}$$ : point $$i$$ observed in image $$j$$
- $$\hat{\mathbf{v}}$$ : corrections (error)
- $$\hat{\mathbf{X}}_i$$ : Estimated 3D location of the point $$i$$ (guess) in homogeneous coordinate
- $$^a\hat{P}_j$$ : Projection matrix
    - $$\mathbf{x}_{ij}$$ : image pixel coordinate
    - $$\mathbf{p}$$ : projection parameters : intrinsic parameters (typically 5)
    - $$\mathbf{q}$$ : distortion
- $$\hat{\lambda}$$ : Scale factor
- $$\Sigma_{x_{ij}}$$ : uncertainty in the image coordinates
  - $$I_j$$ : number of points in image j
  - $$J$$ : number of images

- Encodes the camera __projection__
- Error is the distance between each projected 3D and measured 2D point
- Encodes the __collinearity constraint__
- __Known__ data association (Strong assumption: We know the exact matches)

## Unknonwns
- 3D locations of new points $$\hat{\mathbf{X}}_{i}$$
- 1D scale factor $$\hat{\lambda}_{ij}$$
- 6D exterior orientation (camera location and orientation)
- 5D projection parameters (intrinsic parameters $$\mathbf{p}$$)
- Nonlinear distortion parameters $$\mathbf{q}$$

$$^a\hat{P}_k(x_{ij}, \mathbf{p}, \mathbf{q}) = \hat{K}(x_{ij}, \hat{\mathbf{p}}, \hat{\mathbf{q}})\hat{R}_j[I_3 \vert-\hat{X}_{0j}]$$

- $$\hat{K}(x_{ij}, \hat{\mathbf{p}}, \hat{\mathbf{q}})$$ : Calibration (interior orientation)
- $$\hat{R}_j[I_3 \vert -\hat{X}_{0j}]$$ : Exterior orientation

## Eliminating the Scale Factors

$$
^a\mathbf{x}_{ij}'+^a\hat{\mathbf{v}}_{x_{ij}'}=\hat{\lambda}_{ij} ^a\hat{P}_j(x_{ij}, \mathbf{p}, \mathbf{q})\hat{\mathbf{X}}_i\\
$$

to

$$
^ax_{ij}'+^a\hat{v}_{x_{ij}'}=\frac{^a\hat{P}_{1:2,j}(x_{ij}, \mathbf{p}, \mathbf{q})\hat{\mathbf{X}}_i}{^a\hat{P}_{3,j}(x_{ij}, \mathbf{p}, \mathbf{q})\hat{\mathbf{X}}_i}\\
$$

By diving the first two elements by third element (Homogeneous coordinates to __Euclidean__ coordinates - Drastically reduce unknowns)


## Setting Up and Solving the System of Normal Equations

- Standard procedure in least squares estimation community
- with unknowns $$\mathbf{x}$$ and observations $$\mathbf{l}$$
- Setting upt the normal equations

$$
A^\top \Sigma^{-1} A \Delta\mathbf{x} = A^\top \Sigma^{-1} \Delta\mathbf{l}
$$

- This yields the estimate
  
$$
\hat{\Delta\mathbf{x}}=(A^\top \Sigma^{-1} A)^{-1} A^\top\Sigma^{-1} \Delta\mathbf{l}
$$

where

- $$A$$ : Jacobian
- $$\Sigma$$ : Information Matrix
- Get the update to the unknown parameters (change), and this way we can solve this by iterative way

## Optimality

- BA is __statistically optimal__ : under some assumptions
- Exploits all observations and considers the uncertainties and correlations
- Exploits all available information
- Computes orientations, calibration parameters, and point locations with highest precision
- Assumes Gaussian noise
- Requires an initial estimate

## Absolute Orientation Through Control Points

Absolute orientation was the task of anchoring the model into the real world

- Reconstruction only with the images gives us so-called photogrammetric model
  - Model that is only defined up to a similarity transform
- We want to know the absolute scale

## Control Points

- Assume that some of the points are control points (known coordinates in the real world)

$$
\mathbf{X}_i + \hat{\mathbf{v}}_{\mathbf{X}_i} = \hat{\mathbf{X}}_i
$$

Provided Coordinates + Correct = Estimated Coordinate

with

$$
\Sigma_{\mathbf{X}_i, \mathbf{X}_i}, i=1,\dots, I_{CP}
$$

$$I_{CP}$$ : Number of control points

We can treat the control points noisy or noise free under different objective functions.

- __Statistically optimal approach__ : consider that C.P.s are noisy
- __BA with fixed C.P.__ : consider that C.P.s are noise free (enforce the geometry to match the C.P.)
  - Link to a ground truth map
  - Statistically suboptimal approach

## Two Step BA with Control Points

Step 1.

- BA with noisy control points
- Search for gross errors using statistical tests

$$
T = \hat{v}_{X_i}^\top \Sigma_{\hat{v}_{X_i}, \hat{v}_{X_i}}^{-1} \hat{v}_{X_i} ~ \chi_3^2
$$

  - See the uncertainties of the control points aligned with defined uncertainty
- Eliminate erroneous control points

Step 2.

- BA with fixed control points

## How Many Control Points?

- For DLT or P3P solutions, we need 3~6 control points per image pair
- Only a small number of control points is needed
- Typically full C.P.s at the boundaries

## Initial Guess

As all the nonlinear optimization problems need it, BA also requires some initial guesses

- Orientation of the image pair with a direct method
- __No direct solution is known for N views__
- Direct solution means we don't need an initial guess
- We can use those tools for initial guess (successive measure)
  
## Computing an Initial Guess

- Compute relative orientation from the first image pair
- Compute orientation for subsequent images through P3P/RRS
  
__Problems__
- We might have a good chance of loosing track between images
- Cross error handling is essential
- Requres enough new points in each image overlap
- No singular/critical configurations

## Outliers

## Gross Errors / Outliers

- Reasons for gross errors
  - __Wrong correspondences__
  - Wrong point measurement

__How many observations per point do we need to deal with outliers?__
  
- At least 4 views to identify the observation with a gross error
- Observed points from 5~6 different views typically yield good estimates

## Eliminating Gross Errors

- Decompose the problem into several small blocks of 3~6 images first
- Check for gross errors in the small blocks using statistical tests
- Only consider features that can be tracked consistently in all 3~6 images
- Relative orientation (5 point algorithm) combined with RANSAC
- Eliminate gross errors, and then run full BA

## Robust Kernels

- Instead of using a Gaussian noise model, consider a robustified version
- Reduce "penalty" far away zero

![BA_image](../../../assets/images/BA_pt1/Kernels.png)

- A Robust kernel leads to a weight in LS

## Quality of Result

## Precision

- We can compute the theoretical precision 

$$
\Sigma_{\hat{x}\hat{x}}=(A^\top \Sigma_{ll}^{-1}A)^{-1}
$$

- as well as the emperical precision through the variance factor

$$
\hat{\Sigma}_{\hat{x}\hat{x}} = \hat{\sigma}_0^2(A^\top\Sigma_{ll}^{-1}A)^{-1}
$$

## Variance Factor

- Does a variance factor take a value around 1?
  - $$\hat{\sigma}_0=1$$ suggests a correct model
- Use a __statistical test__ to judge the variance factor
- In practice, a __F-test often fails__ due to the high redundancy

$$
T = \frac{\hat{\sigma}_l^2}{\underline{\sigma}^2} ~ F(R, \infty), e.g., R = 10000
$$

- __The uncertainty in the assumed measurement noise has to be considered__
  - Can use the F-test by changing the redundancy based on the measurement uncertainty

## Precision

- With eliminated gross error
- small systematic errors
- and $$\hat{\sigma}_0=1$$
- we obtain a realistic estimate for the precision of the parameters

$$
\hat{\Sigma}_{\hat{x}\hat{x}} = \hat{\sigma}_0^2(A^\top\Sigma_{ll}^{-1}A)^{-1}
$$
