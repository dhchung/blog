---
layout: post
category: slam
description: >
  Unary factor for absolute orientation
---

# [GTSAM] Orientation correction using gravity direction

For attitude correction, we can use the gravity direction which doesn't change(assuming that the gravity field is uniform and the mission area is small enough).

With coordinate system which the positive z is headed towards downward and positive x value is the vehicle heading, the gravity direction is $$\begin{bmatrix}0 & 0& 1\end{bmatrix}^\top$$.

