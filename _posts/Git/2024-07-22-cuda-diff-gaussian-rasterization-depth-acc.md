---
title: "[Git] 3DGS diff-gaussian-rasterization & diff-gaussian-rasterization-depth-acc"
last_modified_at: 2024-07-09
categories:
  - Git
tags:
  - git
  - github
  - cuda programming
  - cuda rasterization
  - diff-gaussian-rasterization
  - diff-gaussian-rasterization-depth-acc
excerpt: "CUDA Rasterization, from . import _C"
use_math: true
classes: wide
comments: true
---

## Original 3D Gaussian Splatting의 submodules/diff-gaussian-rasterization에 depth map을 래스터화하는 모듈을 추가할 수 있습니다.

## [diff-gaussian-rasterization](https://github.com/graphdeco-inria/diff-gaussian-rasterization/tree/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d)

## [diff-gaussian-rasterization-depth-acc](https://github.com/robot0321/diff-gaussian-rasterization-depth-acc/tree/c63d79dc4d59b2965eaf7bada5dda2eae68c08af?tab=readme-ov-file)

위는 둘다 Cuda 코드로 작성되었으며, setup.py에서 `_C`가 빌드되어, diff_gaussian_rasterization 혹은 diff_gaussian_rasterization_depth_acc에서

`from . import _C`로 rasterization관련 `.cu` 쿠다 코드를 가져와서 사용합니다.

### [diff_gaussian_rasterization/\_\_init\_\_.py](https://github.com/robot0321/diff-gaussian-rasterization-depth-acc/blob/c63d79dc4d59b2965eaf7bada5dda2eae68c08af/diff_gaussian_rasterization_depth_acc/__init__.py)

```python
from . import _C
...
        # Invoke C++/CUDA rasterizer
        if raster_settings.debug:
            cpu_args = cpu_deep_copy_tuple(args) # Copy them before they can be corrupted
            try:
                num_rendered, color, depth, acc, radii, geomBuffer, binningBuffer, imgBuffer = _C.rasterize_gaussians(*args)
            except Exception as ex:
                torch.save(cpu_args, "snapshot_fw.dump")
                print("\nAn error occured in forward. Please forward snapshot_fw.dump for debugging.")
                raise ex
        else:
            num_rendered, color, depth, acc, radii, geomBuffer, binningBuffer, imgBuffer = _C.rasterize_gaussians(*args)

        # Keep relevant tensors for backward
        ctx.raster_settings = raster_settings
        ctx.num_rendered = num_rendered
        ctx.save_for_backward(colors_precomp, depth, acc, means3D, scales, rotations, cov3Ds_precomp, radii, sh, geomBuffer, binningBuffer, imgBuffer)
        return color, depth, acc, radii

    @staticmethod
    def backward(ctx, grad_out_color, grad_out_depth, grad_acc, grad_radii):

        # Restore necessary values from context
        num_rendered = ctx.num_rendered
        raster_settings = ctx.raster_settings
        colors_precomp, depth, acc, means3D, scales, rotations, cov3Ds_precomp, radii, sh, geomBuffer, binningBuffer, imgBuffer = ctx.saved_tensors

        # Restructure args as C++ method expects them
        args = (raster_settings.bg,
                means3D, 
                radii,
                acc,
                depth,
                colors_precomp, 
                scales, 
                rotations, 
                raster_settings.scale_modifier, 
                cov3Ds_precomp, 
                raster_settings.viewmatrix, 
                raster_settings.projmatrix, 
                raster_settings.tanfovx, 
                raster_settings.tanfovy, 
                grad_out_color,
                grad_out_depth, 
                sh, 
                raster_settings.sh_degree, 
                raster_settings.campos,
                geomBuffer,
                num_rendered,
                binningBuffer,
                imgBuffer,
                raster_settings.debug)
...
```

### [diff_gaussian_rasterization_depth_acc/\_\_init\_\_.py](https://github.com/graphdeco-inria/diff-gaussian-rasterization/blob/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d/diff_gaussian_rasterization/__init__.py)

- depth가 추가된 모듈의 경우 depth, acc도 같이 rasterize 합니다.


```python
from . import _C
...
        # Invoke C++/CUDA rasterizer
        if raster_settings.debug:
            cpu_args = cpu_deep_copy_tuple(args) # Copy them before they can be corrupted
            try:
                num_rendered, color, radii, geomBuffer, binningBuffer, imgBuffer = _C.rasterize_gaussians(*args)
            except Exception as ex:
                torch.save(cpu_args, "snapshot_fw.dump")
                print("\nAn error occured in forward. Please forward snapshot_fw.dump for debugging.")
                raise ex
        else:
            num_rendered, color, radii, geomBuffer, binningBuffer, imgBuffer = _C.rasterize_gaussians(*args)

        # Keep relevant tensors for backward
        ctx.raster_settings = raster_settings
        ctx.num_rendered = num_rendered
        ctx.save_for_backward(colors_precomp, means3D, scales, rotations, cov3Ds_precomp, radii, sh, geomBuffer, binningBuffer, imgBuffer)
        return color, radii

    @staticmethod
    def backward(ctx, grad_out_color, _):

        # Restore necessary values from context
        num_rendered = ctx.num_rendered
        raster_settings = ctx.raster_settings
        colors_precomp, means3D, scales, rotations, cov3Ds_precomp, radii, sh, geomBuffer, binningBuffer, imgBuffer = ctx.saved_tensors

        # Restructure args as C++ method expects them
        args = (raster_settings.bg,
                means3D, 
                radii, 
                colors_precomp, 
                scales, 
                rotations, 
                raster_settings.scale_modifier, 
                cov3Ds_precomp, 
                raster_settings.viewmatrix, 
                raster_settings.projmatrix, 
                raster_settings.tanfovx, 
                raster_settings.tanfovy, 
                grad_out_color, 
                sh, 
                raster_settings.sh_degree, 
                raster_settings.campos,
                geomBuffer,
                num_rendered,
                binningBuffer,
                imgBuffer,
                raster_settings.debug)
...
```

## Rasterization 관련된 Cuda 코드는 `cuda_rasterizer/xxx.cu`, `rasterize_points.cu` 관련 코드를 변경하여 작성하고 `setup.py`에서 build 합니다.

- 실제로 `cuda_rasterizer/rasterizer_impl.cu`, `cuda_rasterizer/forward.cu`, `cuda_rasterizer.backward.cu`, `rasterize_points.cu` 코드에 모두 depth를 rasterize하기 위한 코드가 추가되었음을 확인해볼 수 있습니다.
- `diff-gaussian-rasterization-depth-acc`에 해당하는 cuda 코드는 `out_depth`, `out_acc`와 같은 depth 관련 텐서가 추가적으로 생성되었고, backpropagation 계산에 `accum_acc`, `accum_depth` 인자를 포함합니다.

### [diff-gaussian-rasterization/setup.py](https://github.com/graphdeco-inria/diff-gaussian-rasterization/blob/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d/setup.py)

```python
#
# Copyright (C) 2023, Inria
# GRAPHDECO research group, https://team.inria.fr/graphdeco
# All rights reserved.
#
# This software is free for non-commercial, research and evaluation use 
# under the terms of the LICENSE.md file.
#
# For inquiries contact  george.drettakis@inria.fr
#

from setuptools import setup
from torch.utils.cpp_extension import CUDAExtension, BuildExtension
import os
os.path.dirname(os.path.abspath(__file__))

setup(
    name="diff_gaussian_rasterization",
    packages=['diff_gaussian_rasterization'],
    ext_modules=[
        CUDAExtension(
            name="diff_gaussian_rasterization._C",
            sources=[
            "cuda_rasterizer/rasterizer_impl.cu",
            "cuda_rasterizer/forward.cu",
            "cuda_rasterizer/backward.cu",
            "rasterize_points.cu",
            "ext.cpp"],
            extra_compile_args={"nvcc": ["-I" + os.path.join(os.path.dirname(os.path.abspath(__file__)), "third_party/glm/")]})
        ],
    cmdclass={
        'build_ext': BuildExtension
    }
)
```


### [diff-gaussian-rasterization-depth-acc/setup.py](https://github.com/robot0321/diff-gaussian-rasterization-depth-acc/blob/c63d79dc4d59b2965eaf7bada5dda2eae68c08af/setup.py)

```python
#
# Copyright (C) 2023, Inria
# GRAPHDECO research group, https://team.inria.fr/graphdeco
# All rights reserved.
#
# This software is free for non-commercial, research and evaluation use 
# under the terms of the LICENSE.md file.
#
# For inquiries contact  george.drettakis@inria.fr
#

from setuptools import setup
from torch.utils.cpp_extension import CUDAExtension, BuildExtension
import os
os.path.dirname(os.path.abspath(__file__))

setup(
    name="diff_gaussian_rasterization_depth_acc",
    packages=['diff_gaussian_rasterization_depth_acc'],
    ext_modules=[
        CUDAExtension(
            name="diff_gaussian_rasterization_depth_acc._C",
            sources=[
            "cuda_rasterizer/rasterizer_impl.cu",
            "cuda_rasterizer/forward.cu",
            "cuda_rasterizer/backward.cu",
            "rasterize_points.cu",
            "ext.cpp"],
            extra_compile_args={"nvcc": ["-I" + os.path.join(os.path.dirname(os.path.abspath(__file__)), "third_party/glm/")]})
        ],
    cmdclass={
        'build_ext': BuildExtension
    }
)
```
