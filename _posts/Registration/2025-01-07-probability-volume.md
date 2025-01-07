---
title: "[Registration] Probability volume, depth map inference, per-pixel depth estimation, estimation confidence, depth hypotheses"
last_modified_at: 2025-01-05
categories:
  - Registration
tags:
  - Registration
  - 정합
  - probability volume
  - depth map inference
  - per-pixel depth estimation
  - estimation confidence
  - cost volume
  - MVSNet
  - depth hypotheses
excerpt: "Probability volume, depth map inference, per-pixel depth estimation, estimation confidence"
use_math: true
classes: wide
comments: true
---

> Reference

>[MVSNet: Depth Inference for Unstructured Multi-view Stereo](https://openaccess.thecvf.com/content_ECCV_2018/papers/Yao_Yao_MVSNet_Depth_Inference_ECCV_2018_paper.pdf)

Probability volume으로부터 depth map을 얻을 수 있습니다.

### _the depth map retrieved from the probability volume_

### Cost Volume

build a 3D cost volume from the extracted feature maps and input cameras.

#### Differential Homography

All feature maps are warped into different fronto-parallel planes of the reference camera to form $N$ feature volumes ${V_i}^N_{i=1}$. 

The coordinate mapping from the warped feature map $V_i(d)$ to $F_i$ at depth $d$ is determined by the planar transformation $x′ ∼ H_i(d)$ · x, where ‘∼’ denotes the projective equality and $H_i(d)$ the homography between the $i^{th}$ feature map and the reference feature map at depth $d$.

#### Cost Metric 

Next, we aggregate multiple feature volumes ${V_i}^N_{i=1}$ to one cost volume $C$.

...

In contrast, our variance-based cost metric explicitly measures the multi-view feature difference.

#### Cost Volume Regularization

We finally apply the softmax operation along the depth direction for probability normalization.

The resulting probability volume is highly desirable in depth map inference that it can not only be used for per-pixel depth estimation, but also for measuring the estimation confidence.

We will show in Sec. 3.3 that **one can easily determine the depth reconstruction quality by analyzing its probability distribution**, which leads to a very concise yet effective outlier filtering strategy in Sec. 4.2.

### Depth Map

#### Initial Estimation

The simplest way to retrieve depth map $D$ from the probability volume $P$ is the pixel-wise winner-take-all [5] (i.e., $argmax$). 

However, **the $argmax$ operation is unable to produce sub-pixel estimation, and cannot be trained with back-propagation due to its indifferentiability. **
Instead, **we compute the expectation value along the depth direction, i.e., the probability weighted sum over all hypotheses**:

$$
D = \sum_{d=d_{\text{min}}}^{d_{\text{max}}} d \times P(d)
$$

**Where $P(d)$ is the probability estimation for all pixels at depth $d$.**

Note that this operation is also referred to as the $soft \ argmin$ operation in [17]. It is fully differentiable and able to approximate the argmax result.

**While the depth hypotheses are uniformly sampled within range [dmin, dmax] during cost volume construction, the expectation value here is able to produce a continuous depth estimation.**

The output depth map (Fig. 2 (b)) is of the same size to 2D image feature maps, which is downsized by four in each dimension compared to input images.

#### Probability Map

**The probability distribution along the depth direction also reflects the depth estimation quality.** 

Although the multi-scale 3D CNN has very strong ability to regularize the probability to the single modal distribution, we notice that for those falsely matched pixels, their probability distributions are scattered and cannot be concentrated to one peak (see Fig. 2 (c)). 

**Based on this observation, we define the quality of a depth estimation $\hat{d}$ as the probability that the ground truth depth is within a small range near the estimation.** 

**As depth hypotheses are discretely sampled along the camera frustum, we simply take the probability sum over the four nearest depth hypotheses to measure the estimation quality.** 

Notice that other statistical measurements, such as standard deviation or entropy can also be used here, but in our experiments we observe no significant improvement from these measurements for depth map filtering. Moreover, our probability sum formulation leads to a better control of thresholding parameter for outliers filtering.

### Depth Map Refinement

_the depth map retrieved from the probability volume_

While **the depth map retrieved from the probability volume** is a qualified output, the reconstruction boundaries may suffer from oversmoothing due to the large receptive field involved in the regularization,
which is similar to the problems in semantic segmentation [4] and image matting [37]. 

Notice that the reference image in natural contains boundary information, we thus use the reference image as a guidance to refine the depth map. 

Inspired by the recent image matting algorithm [37], we apply a depth residual learning network at the end of MVSNet. 

The initial depth map and the resized reference image are concatenated as a 4-channel input, which is then passed through three 32-channel 2D convolutional layers followed by one 1-channel convolutional layer to learn the depth residual.

he initial depth map is then added back to generate the refined depth map. 

The last layer does not contain the BN layer and the ReLU unit as to learn the negative residual. Also, to prevent being biased at a certain depth scale, we pre-scale the initial depth magnitude to range [0, 1], and convert it back after the refinement.



