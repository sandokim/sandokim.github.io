---
title: "[3D CV 연구] 3DGS SuGaR sugar_model.py _covert_vertex_colors_to_texture"
last_modified_at: 2024-06-23
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - triangle
  - texture image
  - uv map
  - faces_uv
  - verts_coords
  - verts_uv
  - texture_img
  - offset
excerpt: "3DGS SuGaR sugar_model.py _covert_vertex_colors_to_texture"
use_math: true
classes: wide
---

## faces_uv를 정의하여 texture_img로 저장할 때, triangle의 vertices가 서로 겹치지 않게 하기 위하여 4씩 offset을 줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4733b668-1738-4389-9aaf-e8afb3fa1c21)

```python
# SuGaR/sugar_scene/sugar_model.py

def _convert_vertex_colors_to_texture(
    rc:SuGaR, 
    colors:torch.Tensor,
    square_size:int=4,
    ):
    points_to_mesh = rc.points
    
    n_square_per_axis = int(np.sqrt(len(points_to_mesh)) + 1)
    texture_size = square_size * n_square_per_axis
    
    n_features = colors.shape[-1]
    
    point_idx_per_pixel = torch.zeros(texture_size, texture_size, device=rc.device).int()

    with torch.no_grad():
        # Build face UVs
        faces_uv = torch.Tensor(
            [[0, 2, 1], [0, 3, 2]]
            ).to(rc.device)[None] + 4 * torch.arange(len(points_to_mesh), device=rc.device)[:, None, None]
        faces_uv = faces_uv.view(-1, 3).long()

        # Build verts UVs
        verts_coords = torch.cartesian_prod(
            torch.arange(n_square_per_axis, device=rc.device), 
            torch.arange(n_square_per_axis, device=rc.device)
            )[:, None] * square_size
        verts_uv = torch.Tensor(
            [[1., 1.], [1., square_size-1], [square_size-1, square_size-1], [square_size-1, 1.]]
            ).to(rc.device)[None] + verts_coords
        verts_uv = verts_uv.view(-1, 2).long()[:4*len(points_to_mesh)] / texture_size

        # Build texture image
        texture_img = torch.zeros(texture_size, texture_size, n_features, device=rc.device)    
        n_squares_filled = 0
        for i in range(n_square_per_axis):
            for j in range(n_square_per_axis):
                if n_squares_filled >= len(points_to_mesh):
                    break
                start_idx_i = i * square_size
                start_idx_j = j * square_size
                texture_img[..., 
                            start_idx_i:start_idx_i + square_size, 
                            start_idx_j:start_idx_j + square_size, :] = colors[i*n_square_per_axis + j].unsqueeze(0).unsqueeze(0)
                point_idx_per_pixel[...,
                                    start_idx_i:start_idx_i + square_size, 
                                    start_idx_j:start_idx_j + square_size] = i*n_square_per_axis + j
                n_squares_filled += 1
                
        texture_img = texture_img.transpose(0, 1)
        texture_img = texture_img.flip(0)
        
        point_idx_per_pixel = point_idx_per_pixel.transpose(0, 1)
        point_idx_per_pixel = point_idx_per_pixel.flip(0)
    
    return faces_uv, verts_uv, texture_img, point_idx_per_pixel
```






