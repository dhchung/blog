---
layout: post
category: slam
description: >
  How to make a custom node and factor for GTSAM c++
---

# [GTSAM] Making simple custom node and factor

## Defining a node
GTSAM objects:
LieGroup $$\subset$$ Manifold $$\subset$$ Testble

Required functions for each sets:

LieGroup:
- Identity, Logmap, Expmap, Compose, Between, Inverse

Manifold:
- dimension, GetDimension, Local, Retract

Testble:
- Equal, Print

Custom node which we are going to make is in Manifold set, so we need to define
- Equal, Print, dimension, GetDimension, Local, Retract




## Defining a factor

Let's say the state is a N dimensional vector, $$
\begin{bmatrix}
s_1&s_2&s_3&\dots&s_N
\end{bmatrix}^\top
$$

and a measurement is a M dimensional vector:$$\begin{bmatrix}
m_1&m_2&m_3&\dots&m_M
\end{bmatrix}^\top
$$

Let's say that the measurement is a between factor which relates two states:

$$
\mathbf{m_k} = h(\mathbf{s_i}, \mathbf{s_j})
$$

For graph SLAM, we need to define the jacobian of measurement with respect to both states, and error between the expected measurement and actual measurement (kinda similar to 'innovation' term in Kalman filter).

So, for the jacobian of the measurement for the base state:

$$
H_1 =\begin{bmatrix}
\frac{\partial m_{k1}}{\partial s_{i1}} & \frac{\partial m_{k1}}{\partial s_{i2}} & \dots & \frac{\partial m_{k1}}{\partial s_{iN}} \\
\frac{\partial m_{k2}}{\partial s_{i1}} & \frac{\partial m_{k2}}{\partial s_{i2}} & \dots & \frac{\partial m_{k2}}{\partial s_{iN}} \\
\vdots & \vdots &  & \vdots \\
\frac{\partial m_{kM}}{\partial s_{i1}} & \frac{\partial m_{kM}}{\partial s_{i2}} & \dots & \frac{\partial m_{kM}}{\partial s_{iN}}
\end{bmatrix}_{M \times N}
$$

and the jacobian for the target state:

$$
H_2 =\begin{bmatrix}
\frac{\partial m_{k1}}{\partial s_{j1}} & \frac{\partial m_{k1}}{\partial s_{j2}} & \dots & \frac{\partial m_{k1}}{\partial s_{jN}} \\
\frac{\partial m_{k2}}{\partial s_{j1}} & \frac{\partial m_{k2}}{\partial s_{j2}} & \dots & \frac{\partial m_{k2}}{\partial s_{jN}} \\
\vdots & \vdots &  & \vdots \\
\frac{\partial m_{kM}}{\partial s_{j1}} & \frac{\partial m_{kM}}{\partial s_{j2}} & \dots & \frac{\partial m_{kM}}{\partial s_{jN}}
\end{bmatrix}_{M \times N}
$$

And the error is defined as:

$$
\mathbf{v} = h(\mathbf{s_i}, \mathbf{s_j}) - \mathbf{m_k}
$$

Per optimization step, each jacobian matrix indicates which direction should the values be changed, and the error vector indicates how much should the values be changed.


This can be adapted to connect between any nodes; let's say there's a N dimensional node $$\mathbf{s}$$, K dimensional node $$\mathbf{r}$$ and a M dimensional measurement which connect between those nodes $$\mathbf{m}$$ (e.g. $$\mathbf{s}$$ is a state, $$\mathbf{m}$$ is a measurement and $$\mathbf{r}$$ is a corresponding landmark).

Similarly:

$$
H_1 =\begin{bmatrix}
\frac{\partial m_{k1}}{\partial s_{i1}} & \frac{\partial m_{k1}}{\partial s_{i2}} & \dots & \frac{\partial m_{k1}}{\partial s_{iN}} \\
\frac{\partial m_{k2}}{\partial s_{i1}} & \frac{\partial m_{k2}}{\partial s_{i2}} & \dots & \frac{\partial m_{k2}}{\partial s_{iN}} \\
\vdots & \vdots &  & \vdots \\
\frac{\partial m_{kM}}{\partial s_{i1}} & \frac{\partial m_{kM}}{\partial s_{i2}} & \dots & \frac{\partial m_{kM}}{\partial s_{iN}}
\end{bmatrix}_{M \times N}, 
H_2 =\begin{bmatrix}
\frac{\partial m_{k1}}{\partial r_{j1}} & \frac{\partial m_{k1}}{\partial r_{j2}} & \dots & \frac{\partial m_{k1}}{\partial r_{jK}} \\
\frac{\partial m_{k2}}{\partial r_{j1}} & \frac{\partial m_{k2}}{\partial r_{j2}} & \dots & \frac{\partial m_{k2}}{\partial r_{jK}} \\
\vdots & \vdots &  & \vdots \\
\frac{\partial m_{kM}}{\partial r_{j1}} & \frac{\partial m_{kM}}{\partial r_{j2}} & \dots & \frac{\partial m_{kM}}{\partial r_{jK}}
\end{bmatrix}_{M \times K}
$$

and error:

$$
\mathbf{v} = f(\mathbf{s_i}, \mathbf{r_j}) - \mathbf{m_k}
$$

