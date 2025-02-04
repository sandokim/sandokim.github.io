---
title: "[4D] 4D Gaussian Splatting Research"
last_modified_at: 2025-01-15
categories:
  - 4D
tags:
  - 4D
  - 4DGS
  - 4D Gaussian Splatting
excerpt: "4D Gaussian Splatting 연구들에 대해 알아봅시다."
use_math: true
classes: wide
comments: true
---

[Wu, Guanjun, et al. "4d gaussian splatting for real-time dynamic scene rendering." Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition. 2024.](https://guanjunwu.github.io/4dgs/)

![image](https://github.com/user-attachments/assets/1b849f6f-e254-4060-8be6-9d7023de590c)

<이미지 출처 4D Gaussian Splatting>

## Related Works

### 2.1. Novel View Synthesis

- [9] proposes to use an explicit voxel grid to model temporal information, accelerating the learning time for dynamic scenes to half an hour and applied in [19, 32, 62]. The proposed deformation-based neural rendering methods are shown in Fig. 2 (a).
  - [9] Jiemin Fang, Taoran Yi, Xinggang Wang, Lingxi Xie, Xiaopeng Zhang, Wenyu Liu, Matthias Nießner, and Qi Tian. **Fast dynamic radiance fields with time-aware neural voxels**. In SIGGRAPH Asia 2022 Conference Papers, pages 1–9, 2022. 1, 2, 4, 5, 6, 7, 12, 13, 14, 15
  - [19] Xiang Guo, Jiadai Sun, Yuchao Dai, Guanying Chen, Xiaoqing Ye, Xiao Tan, Errui Ding, Yumeng Zhang, and Jingdong Wang. **Forward flow for novel view synthesis of dynamic scenes.** In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 16022–16033, 2023. 2, 5, 6, 7, 12
  - [32] Yu-Lun Liu, Chen Gao, Andreas Meuleman, Hung-Yu Tseng, Ayush Saraf, Changil Kim, Yung-Yu Chuang, Johannes Kopf, and Jia-Bin Huang. **Robust dynamic radiance fields**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 13–23, 2023. 2
  - [62] Taoran Yi, Jiemin Fang, Xinggang Wang, and Wenyu Liu. **Generalizable neural voxels for fast human radiance fields**. arXiv preprint arXiv:2303.15387, 2023. 2

- Flow-based [14, 28, 32, 52, 67] methods adopting warping algorithm to synthesis novel views by blending nearby frames.
  - [14] Chen Gao, Ayush Saraf, Johannes Kopf, and Jia-Bin Huang. **Dynamic view synthesis from dynamic monocular video**. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 5712–5721, 2021. 2
  - [28] Zhengqi Li, Simon Niklaus, Noah Snavely, and OliverWang. **Neural scene flow fields for space-time view synthesis of dynamic scenes**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 6498– 6508, 2021. 2
  - [32] Yu-Lun Liu, Chen Gao, Andreas Meuleman, Hung-Yu Tseng, Ayush Saraf, Changil Kim, Yung-Yu Chuang, Johannes Kopf, and Jia-Bin Huang. **Robust dynamic radiance fields**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 13–23, 2023. 2
  - [52] Fengrui Tian, Shaoyi Du, and Yueqi Duan. **Mononerf: Learning a generalizable dynamic radiance field from monocular videos**. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 17903–17913, 2023. 2
  - [67] Kaichen Zhou, Jia-Xing Zhong, Sangyun Shin, Kai Lu, Yiyuan Yang, Andrew Markham, and Niki Trigoni. **Dynpoint: Dynamic neural point for view synthesis**. Advances in Neural Information Processing Systems, 36, 2024. 2, 3

- [5, 12, 13, 25, 48, 53] represent further advancements in faster dynamic scene learning by adopting decomposed neural voxels. They treat sampled points in each timestamp individually as shown in Fig. 2 (b).
  - [5] Ang Cao and Justin Johnson. **Hexplane: A fast representation for dynamic scenes**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 130–141, 2023. 1, 2, 4, 5, 6, 7, 13, 14
  - [12] Sara Fridovich-Keil, Giacomo Meanti, Frederik Rahbæk Warburg, Benjamin Recht, and Angjoo Kanazawa. **K-planes: Explicit radiance fields in space, time, and appearance**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 12479–12488, 2023. 1, 2, 4, 5, 6, 7, 13, 14
  - [13] Wanshui Gan, Hongbin Xu, Yi Huang, Shifeng Chen, and Naoto Yokoya. **V4d: Voxel for 4d novel view synthesis**. IEEE Transactions on Visualization and Computer Graphics, 2023. 2, 6, 7
  - [25] Tianye Li, Mira Slavcheva, Michael Zollhoefer, Simon Green, Christoph Lassner, Changil Kim, Tanner Schmidt, Steven Lovegrove, Michael Goesele, Richard Newcombe, et al. **Neural 3d video synthesis from multi-view video**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 5521–5531, 2022. 2, 6, 7, 12, 13, 15
  - [48] Ruizhi Shao, Zerong Zheng, Hanzhang Tu, Boning Liu, Hongwen Zhang, and Yebin Liu. **Tensor4d: Efficient neural 4d decomposition for high-fidelity dynamic reconstruction and rendering**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 16632– 16642, 2023. 1, 2, 4
  - [53] Feng Wang, Zilong Chen, Guokang Wang, Yafei Song, and Huaping Liu. **Masked space-time hash encoding for efficient dynamic scene reconstruction**. Advances in neural information processing systems, 2023. 2, 5, 6, 7

- [16, 30, 41, 54, 56, 58] are efficient methods to handle multi-view setups.
  - [16] Xiangjun Gao, Jiaolong Yang, Jongyoo Kim, Sida Peng, Zicheng Liu, and Xin Tong. **Mps-nerf: Generalizable 3d human rendering from multiview images**. IEEE Transactions on Pattern Analysis and Machine Intelligence, 2022. 2 
  - [30] Haotong Lin, Sida Peng, Zhen Xu, Tao Xie, Xingyi He, Hujun Bao, and Xiaowei Zhou. **High-fidelity and real-time novel view synthesis for dynamic scenes**. In SIGGRAPH Asia Conference Proceedings, 2023. 2, 5, 6, 7, 9, 15
  - [41] Sida Peng, Yunzhi Yan, Qing Shuai, Hujun Bao, and Xiaowei Zhou. **Representing volumetric videos as dynamic mlp maps**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 4252– 4262, 2023. 2
  - [54] Feng Wang, Sinan Tan, Xinghang Li, Zeyue Tian, Yafei Song, and Huaping Liu. **Mixed neural voxels for fast multiview video synthesis**. In Proceedings of the IEEE/CVF International Conference on Computer Vision, pages 19706– 19716, 2023. 2, 5, 13
  - [58] Qingshan Xu,Weihang Kong,Wenbing Tao, and Marc Pollefeys. **Multi-scale geometric consistency guided and planar prior assisted multi-view stereo**. IEEE Transactions on Pattern Analysis and Machine Intelligence, 45(4):4945–4963, 2022. 2

_The aforementioned methods though achieve fast training speed, real-time rendering for dynamic scenes is still challenging, especially for monocular input._

- 4D Gaussian Splatting aims at constructing a highly efficient training and rendering pipeline in Fig. 2 (c), while maintaining the quality, even for sparse inputs.

### 4.2 Gaussiann Deformation Field Network
The network to learn the Gaussian deformation field includes an efficient spatial-temporal structure encoder $H$ and a Gaussian deformation decoder $D$ for predicting the defromation of each 3D Gaussian.

- The deformation of 3D Gaussians $\Delta G$
- Gaussian deformation field network $F$
- the deformed 3D Gaussians $G'$
- the spatial-temporal structure encoder $H$
- the multi-head Gaussian deformation Decoder $D$

$$
G' = \Delta G + G 
$$

$$
f_d = H(G,t) 
$$

$$
\Delta G = D(f)
$$

$$
\Delta G = F(G,t)
$$

#### Spatial-Temporal Structure Encoders.
Nearby 3D Gaussians always share similar spatial and temporal information. To model 3D Gaussians' features effectively, we introduce an efficient spatial-temporal struture encoder $H$ including a multi-resolution HexPlane $R(i,j)$ and a tiny MLP $\phi_d$ inspired by [5,9,12,48]

- [5] Ang Cao and Justin Johnson. **Hexplane: A fast representation for dynamic scenes**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 130–141, 2023. 1, 2, 4, 5, 6, 7, 13, 14
- [9] Jiemin Fang, Taoran Yi, Xinggang Wang, Lingxi Xie, Xiaopeng Zhang, Wenyu Liu, Matthias Nießner, and Qi Tian. **Fast dynamic radiance fields with time-aware neural voxels**. In SIGGRAPH Asia 2022 Conference Papers, pages 1–9, 2022. 1, 2, 4, 5, 6, 7, 12, 13, 14, 15
- [12] Sara Fridovich-Keil, Giacomo Meanti, Frederik Rahbæk Warburg, Benjamin Recht, and Angjoo Kanazawa. **K-planes: Explicit radiance fields in space, time, and appearance**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 12479–12488, 2023. 1, 2, 4, 5, 6, 7, 13, 14
- [48] Ruizhi Shao, Zerong Zheng, Hanzhang Tu, Boning Liu, Hongwen Zhang, and Yebin Liu. **Tensor4d: Efficient neural 4d decomposition for high-fidelity dynamic reconstruction and rendering**. In Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 16632– 16642, 2023. 1, 2, 4

**While the vanilla 4D neural voxel is memory-consuming, we adopt a 4D K-Planes [12] module to decompose the 4D neural voxel into 6 multi-resolution planes.**

**All 3D Gaussians in a certain area can be contained in the bounding plane voxels and the deformation of Gaussians can also be encoded in nearby temporal voxels.**

Specifically, the spatial-temporal structure encoder $H$ contains 6 multi-resolution plane modules $R_l(i,j)$ and a tiny MLP $\phi_d$, i.e. $H(G,t) = \{R_l(i,j), \phi_d | (i,j) \in {(x,y),(x,z),(y,z),(x,t),(y,t),(z,t)}, l \in {1,2}\}.$

The position $\mu = (x,y,z)$ is the mean value of 3D Gaussians $G$.

Each voxel module is defined by $R(i,j) \in \mathcal{R}^{h \times lN_i \times lN_j}$, where $h$ stands for the hidden dim of features, and $N$ denotes the basic resolution of voxel grid and $l$ equals to the upsampling scale.

- $N$: the basic resolution of voxel grid
- $l$: the upsampling scale

This entails encoding information of the 3D Gaussians within the 6 2D voxel planes while considering temporal information.

The formula for **computing seperate voxel features** is as follows:

$$
f_h = \bigcup_l \prod \text{interp}(R_l(i,j)), 
$$
$$
(i,j) \in \{(x,y),(x,z),(y,z),(x,t),(y,t),(z,t)\}
$$

$f_h \in \mathcal{R}^{h * l}$ is the feature of neural voxels. 'interp' denotes the bilinear interpolation for querying the voxel features located at 4 vertices of the grid.

The discussion of the production process is similar to K-Planes [12].

Then a tiny MLP $\phi_d$ merges all the features by $f_d = \phi_d(f_h)$.


