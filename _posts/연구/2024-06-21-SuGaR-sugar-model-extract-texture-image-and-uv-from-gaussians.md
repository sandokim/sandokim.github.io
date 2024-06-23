---
title: "[3D CV 연구] 3DGS SuGaR sugar_model.py extract_texture_image_and_uv_from_gaussians"
last_modified_at: 2024-06-21
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - extract_texture_image_and_uv_from_gaussians
  - barycentric coordinates
  - 무게중심 좌표
  - triangle
  - texture image
  - uv map
excerpt: "3DGS SuGaR sugar_model.py extract_texture_image_and_uv_from_gaussians"
use_math: true
classes: wide
---

### UV map에 texture image를 대응시킬 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d4f5b04b-77e1-44a3-8a21-de2e313691cd)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5abc4d7f-60f7-4312-a04d-1711f9f158de)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/88af76c7-78dc-4c0f-bf02-bd1bf10d22cf)

### UV mapping이란 뭘까요? 
UV mapping is the 3D modeling process of projecting a 3D model's surface to a 2D image for texture mapping. The letters "U" and "V" denote the axes of the 2D texture because "X", "Y", and "Z" are already used to denote the axes of the 3D object in model space.

### UV mapping에서 가장 기억해야할 점
- ***The UV mapping process involves assigning pixels in the image to surface mappings on the polygon.***
- ***When a model is created as a polygon mesh using a 3D modeller, UV coordinates (also known as texture coordinates) can be generated for each vertex in the mesh.***
- **Once the model is unwrapped, the artist can paint a texture on each triangle individually, using the unwrapped mesh as a template.**
- **UV coordinates are optionally applied per face.[2]** **This means a shared spatial vertex position can have different UV coordinates for each of its triangles, so adjacent triangles can be cut apart and positioned on different areas of the texture map.**
- [출처: 위키피디아 UV Mapping 설명](https://en.wikipedia.org/wiki/UV_mapping)

### SuGaR를 사용하여 gaussian들로부터 texture image와 uv가 어떻게 extract되는지 알아봅시다.

- 먼저 다음 사진은 10000 x 10000 의 픽셀을 가지는 texture image입니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a50eddc9-c908-4a0c-9739-b2ea54a9afda)
- 이 texture image는 먼저 정의한 uv coordinates위의 좌표에서 정의되었을 것입니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7c1cdac8-1e89-4f20-b204-f0e66309318a)
- 그리고 이 texture는 gaussian render를 사용하여 만들어졌습니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1cf04a18-d705-4117-977e-637b7afbc0b1)

### gaussians renderers로 만들어진 texture가 어떻게 만들어졌는지 더 자세히 살펴봅시다.

- 먼저 blender에서 OBJ 파일을 열어주고, Material Preview 모드로 바꿔줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3ee1d753-2de0-497f-ad9f-ec69a0513d7c)
- 그리고 우측하단에 우클릭을 하여 Scene Statistics를 켜줘서, Vertices, Faces의 개수를 확인할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ee2a9686-96ec-4788-aedd-0f66a7fac644)
- 우측하단에서 Verts: 1,023,699개 / Faces: 1,997,996개임을 확익할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/826badf1-dd51-46db-be94-20f6f91c06bf)
- 결론부터 말하면, refined_mesh의 faces는 1,997,996개 vertices는 1,025,032개인걸 감안해서 default sqaure_size가 10일 때, uv map 크기가 10000x10000로 나온 것인데 어떻게 계산된 것인지 아래의 `sugar_model.py`에서 `extract_texture_image_and_uv_from_gaussian` 함수를 보고 이해해봅시다.
- 먼저 전체 코드를 쭉 훑어보세요.
  
```python
def extract_texture_image_and_uv_from_gaussians(
    rc:SuGaR,
    square_size:int=10,
    n_sh=-1,
    texture_with_gaussian_renders=True,
    ):
    
    from pytorch3d.renderer import (
    AmbientLights,
    MeshRenderer,
    SoftPhongShader,
    )
    from pytorch3d.renderer.blending import BlendParams
    
    if square_size < 3:
        raise ValueError("square_size must be >= 3")
    
    surface_mesh = rc.surface_mesh
    verts = surface_mesh.verts_list()[0]
    faces = surface_mesh.faces_list()[0]
    faces_verts = verts[faces]
    
    n_triangles = len(faces)
    n_gaussians_per_triangle = rc.n_gaussians_per_surface_triangle
    n_squares = n_triangles // 2 + 1
    n_square_per_axis = int(np.sqrt(n_squares) + 1)
    texture_size = square_size * (n_square_per_axis)
    
    if n_sh==-1:
        n_sh = rc.sh_coordinates.shape[1]
    faces_features = rc.sh_coordinates[:, :n_sh].reshape(n_triangles, n_gaussians_per_triangle, n_sh * 3)
    n_features = faces_features.shape[-1]
    
    if texture_with_gaussian_renders:
        n_features = 3
    
    # Build faces UV.
    # Each face will have 3 corresponding vertices in the UV map
    faces_uv = torch.arange(3 * n_triangles, device=rc.device).view(n_triangles, 3)  # n_triangles, 3
    
    # Build corresponding vertices UV
    vertices_uv = torch.cartesian_prod(
        torch.arange(n_square_per_axis, device=rc.device), 
        torch.arange(n_square_per_axis, device=rc.device))
    bottom_verts_uv = torch.cat(
        [vertices_uv[n_square_per_axis:-1, None], vertices_uv[:-n_square_per_axis-1, None], vertices_uv[n_square_per_axis+1:, None]],
        dim=1)
    top_verts_uv = torch.cat(
        [vertices_uv[1:-n_square_per_axis, None], vertices_uv[:-n_square_per_axis-1, None], vertices_uv[n_square_per_axis+1:, None]],
        dim=1)
    
    vertices_uv = torch.cartesian_prod(
        torch.arange(n_square_per_axis, device=rc.device), 
        torch.arange(n_square_per_axis, device=rc.device))[:, None]
    u_shift = torch.tensor([[1, 0]], dtype=torch.int32, device=rc.device)[:, None]
    v_shift = torch.tensor([[0, 1]], dtype=torch.int32, device=rc.device)[:, None]
    bottom_verts_uv = torch.cat(
        [vertices_uv + u_shift, vertices_uv, vertices_uv + u_shift + v_shift],
        dim=1)
    top_verts_uv = torch.cat(
        [vertices_uv + v_shift, vertices_uv, vertices_uv + u_shift + v_shift],
        dim=1)
    
    verts_uv = torch.cat([bottom_verts_uv, top_verts_uv], dim=1)
    verts_uv = verts_uv * square_size
    verts_uv[:, 0] = verts_uv[:, 0] + torch.tensor([[-2, 1]], device=rc.device)
    verts_uv[:, 1] = verts_uv[:, 1] + torch.tensor([[2, 1]], device=rc.device)
    verts_uv[:, 2] = verts_uv[:, 2] + torch.tensor([[-2, -3]], device=rc.device)
    verts_uv[:, 3] = verts_uv[:, 3] + torch.tensor([[1, -1]], device=rc.device)
    verts_uv[:, 4] = verts_uv[:, 4] + torch.tensor([[1, 3]], device=rc.device)
    verts_uv[:, 5] = verts_uv[:, 5] + torch.tensor([[-3, -1]], device=rc.device)
    verts_uv = verts_uv.reshape(-1, 2) / texture_size
    
    # ---Build texture image
    # Start by computing pixel indices for each triangle
    texture_img = torch.zeros(texture_size, texture_size, n_features, device=rc.device)    
    pixel_idx_inside_bottom_triangle = torch.zeros(0, 2, dtype=torch.int32, device=rc.device)
    pixel_idx_inside_top_triangle = torch.zeros(0, 2, dtype=torch.int32, device=rc.device)
    for tri_i in range(0, square_size-1):
        for tri_j in range(0, tri_i+1):
            pixel_idx_inside_bottom_triangle = torch.cat(
                [pixel_idx_inside_bottom_triangle, torch.tensor([[tri_i, tri_j]], dtype=torch.int32, device=rc.device)], dim=0)
    for tri_i in range(0, square_size):
        for tri_j in range(tri_i+1, square_size):
            pixel_idx_inside_top_triangle = torch.cat(
                [pixel_idx_inside_top_triangle, torch.tensor([[tri_i, tri_j]], dtype=torch.int32, device=rc.device)], dim=0)
    
    bottom_triangle_pixel_idx = torch.cartesian_prod(
        torch.arange(n_square_per_axis, device=rc.device), 
        torch.arange(n_square_per_axis, device=rc.device))[:, None] * square_size + pixel_idx_inside_bottom_triangle[None]
    top_triangle_pixel_idx = torch.cartesian_prod(
        torch.arange(n_square_per_axis, device=rc.device), 
        torch.arange(n_square_per_axis, device=rc.device))[:, None] * square_size + pixel_idx_inside_top_triangle[None]
    triangle_pixel_idx = torch.cat(
        [bottom_triangle_pixel_idx[:, None], 
        top_triangle_pixel_idx[:, None]],
        dim=1).view(-1, bottom_triangle_pixel_idx.shape[-2], 2)[:n_triangles]
    
    # Then we compute the barycentric coordinates of each pixel inside its corresponding triangle
    bottom_triangle_pixel_bary_coords = pixel_idx_inside_bottom_triangle.clone().float()
    bottom_triangle_pixel_bary_coords[..., 0] = -(bottom_triangle_pixel_bary_coords[..., 0] - (square_size - 2))
    bottom_triangle_pixel_bary_coords[..., 1] = (bottom_triangle_pixel_bary_coords[..., 1] - 1)
    bottom_triangle_pixel_bary_coords = (bottom_triangle_pixel_bary_coords + 0.) / (square_size - 3)
    bottom_triangle_pixel_bary_coords = torch.cat(
        [1. - bottom_triangle_pixel_bary_coords.sum(dim=-1, keepdim=True), bottom_triangle_pixel_bary_coords],
        dim=-1)
    top_triangle_pixel_bary_coords = pixel_idx_inside_top_triangle.clone().float()
    top_triangle_pixel_bary_coords[..., 0] = (top_triangle_pixel_bary_coords[..., 0] - 1)
    top_triangle_pixel_bary_coords[..., 1] = -(top_triangle_pixel_bary_coords[..., 1] - (square_size - 1))
    top_triangle_pixel_bary_coords = (top_triangle_pixel_bary_coords + 0.) / (square_size - 3)
    top_triangle_pixel_bary_coords = torch.cat(
        [1. - top_triangle_pixel_bary_coords.sum(dim=-1, keepdim=True), top_triangle_pixel_bary_coords],
        dim=-1)
    triangle_pixel_bary_coords = torch.cat(
        [bottom_triangle_pixel_bary_coords[None],
        top_triangle_pixel_bary_coords[None]],
        dim=0)  # 2, n_pixels_per_triangle, 3
    
    all_triangle_bary_coords = triangle_pixel_bary_coords[None].expand(n_squares, -1, -1, -1).reshape(-1, triangle_pixel_bary_coords.shape[-2], 3)
    all_triangle_bary_coords = all_triangle_bary_coords[:len(faces_verts)]
    
    pixels_space_positions = (all_triangle_bary_coords[..., None] * faces_verts[:, None]).sum(dim=-2)[:, :, None]
    
    gaussian_centers = rc.points.reshape(-1, 1, rc.n_gaussians_per_surface_triangle, 3)
    gaussian_inv_scaled_rotation = rc.get_covariance(return_full_matrix=True, return_sqrt=True, inverse_scales=True).reshape(-1, 1, rc.n_gaussians_per_surface_triangle, 3, 3)
    
    # Compute the density field as a sum of local gaussian opacities
    shift = (pixels_space_positions - gaussian_centers)
    warped_shift = gaussian_inv_scaled_rotation.transpose(-1, -2) @ shift[..., None]
    neighbor_opacities = (warped_shift[..., 0] * warped_shift[..., 0]).sum(dim=-1).clamp(min=0., max=1e8)
    neighbor_opacities = torch.exp(-1. / 2 * neighbor_opacities) # / rc.n_gaussians_per_surface_triangle
    
    pixel_features = faces_features[:, None].expand(-1, neighbor_opacities.shape[1], -1, -1).gather(
        dim=-2,
        index=neighbor_opacities[..., None].argmax(dim=-2, keepdim=True).expand(-1, -1, -1, 3)
        )[:, :, 0, :]
        
    # pixel_alpha = neighbor_opacities.sum(dim=-1, keepdim=True)
    texture_img[(triangle_pixel_idx[..., 0], triangle_pixel_idx[..., 1])] = pixel_features

    texture_img = texture_img.transpose(0, 1)
    texture_img = SH2RGB(texture_img.flip(0))
    
    faces_per_pixel = 1
    max_faces_per_bin = 50_000
    mesh_raster_settings = RasterizationSettings(
        image_size=(rc.image_height, rc.image_width),
        blur_radius=0.0, 
        faces_per_pixel=faces_per_pixel,
        # max_faces_per_bin=max_faces_per_bin
    )
    lights = AmbientLights(device=rc.device)
    rasterizer = MeshRasterizer(
            cameras=rc.nerfmodel.training_cameras.p3d_cameras[0], 
            raster_settings=mesh_raster_settings,
    )
    renderer = MeshRenderer(
        rasterizer=rasterizer,
        shader=SoftPhongShader(
            device=rc.device, 
            cameras=rc.nerfmodel.training_cameras.p3d_cameras[0],
            lights=lights,
            blend_params=BlendParams(background_color=(0.0, 0.0, 0.0)),
        )
    )
    texture_idx = torch.cartesian_prod(
        torch.arange(texture_size, device=rc.device), 
        torch.arange(texture_size, device=rc.device)
        ).reshape(texture_size, texture_size, 2
                    )
    texture_idx = torch.cat([texture_idx, torch.zeros_like(texture_idx[..., 0:1])], dim=-1)
    texture_counter = torch.zeros(texture_size, texture_size, 1, device=rc.device)
    idx_textures_uv = TexturesUV(
        maps=texture_idx[None].float(), #texture_img[None]),
        verts_uvs=verts_uv[None],
        faces_uvs=faces_uv[None],
        sampling_mode='nearest',
        )
    idx_mesh = Meshes(
        verts=[rc.surface_mesh.verts_list()[0]],   
        faces=[rc.surface_mesh.faces_list()[0]],
        textures=idx_textures_uv,
        )
    
    for cam_idx in range(len(rc.nerfmodel.training_cameras)):
        p3d_cameras = rc.nerfmodel.training_cameras.p3d_cameras[cam_idx]
        
        # Render rgb img
        rgb_img = rc.render_image_gaussian_rasterizer(
            camera_indices=cam_idx,
            sh_deg=0,  #rc.sh_levels-1,
            compute_color_in_rasterizer=True,  #compute_color_in_rasterizer,
        ).clamp(min=0, max=1)
        
        fragments = renderer.rasterizer(idx_mesh, cameras=p3d_cameras)
        idx_img = renderer.shader(fragments, idx_mesh, cameras=p3d_cameras)[0, ..., :2]
        # print("Idx img:", idx_img.shape, idx_img.min(), idx_img.max())
        update_mask = fragments.zbuf[0, ..., 0] > 0
        idx_to_update = idx_img[update_mask].round().long() 

        use_average = True
        if not use_average:
            texture_img[(idx_to_update[..., 0], idx_to_update[..., 1])] = rgb_img[update_mask]
        else:
            no_initialize_mask = texture_counter[(idx_to_update[..., 0], idx_to_update[..., 1])][..., 0] != 0
            texture_img[(idx_to_update[..., 0], idx_to_update[..., 1])] = no_initialize_mask[..., None] * texture_img[(idx_to_update[..., 0], idx_to_update[..., 1])]

            texture_img[(idx_to_update[..., 0], idx_to_update[..., 1])] = texture_img[(idx_to_update[..., 0], idx_to_update[..., 1])] + rgb_img[update_mask]
            texture_counter[(idx_to_update[..., 0], idx_to_update[..., 1])] = texture_counter[(idx_to_update[..., 0], idx_to_update[..., 1])] + 1

    if use_average:
        texture_img = texture_img / texture_counter.clamp(min=1)        
    
    return verts_uv, faces_uv, texture_img
```

- 이제 중요한 부분을 차근차근 알아가봅시다.
### 1. UV 좌표 계산
- vertices_uv 생성: 먼저, 각 정사각형 셀의 정점 UV 좌표를 생성합니다. torch.cartesian_prod를 사용하여 각 정점의 좌표를 결정합니다.
```python
n_triangles = 1997996  # 주어진 faces의 개수
n_squares = n_triangles // 2 + 1
n_square_per_axis = int(np.sqrt(n_squares) + 1)  # n_square_per_axis 계산
vertices_uv = torch.cartesian_prod(
    torch.arange(n_square_per_axis, device=rc.device), 
    torch.arange(n_square_per_axis, device=rc.device)
)
```
- n_triangles = 1,997,996이면 n_squares = 998999, 따라서 n_square_per_axis = 1000 (정수로 올림)
- `vertices_uv`는 다음과 같이 생성됩니다.
```css
tensor([[   0,    0],
        [   0,    1],
        [   0,    2],
        ...,
        [ 999,  997],
        [ 999,  998],
        [ 999,  999]], device=rc.device)
```

### 2. faces_uv 생성
- 각 삼각형 면의 UV 좌표 인덱스를 생성합니다. `faces_uv`는 각 삼각형의 정점들이 UV 맵에서 어떤 좌표를 가지는지를 나타냅니다.
```python
faces_uv = torch.arange(3 * n_triangles, device=rc.device).view(n_triangles, 3)
```
- `n_triangles = 1,997,996`일 때, `faces_uv`는 다음과 같이 생성됩니다:

```css
tensor([[       0,        1,        2],
        [       3,        4,        5],
        [       6,        7,        8],
        ...,
        [5993985, 5993986, 5993987],
        [5993988, 5993989, 5993990],
        [5993991, 5993992, 5993993]], device=rc.device)
```

### 3. vertices_uv 정리 및 조정
- 각 정사각형 셀을 두 개의 삼각형 (Top과 Bottom)으로 나누고, 그 정점들의 UV 좌표를 계산합니다.

```python
u_shift = torch.tensor([[1, 0]], dtype=torch.int32, device=rc.device)[:, None]
v_shift = torch.tensor([[0, 1]], dtype=torch.int32, device=rc.device)[:, None]
bottom_verts_uv = torch.cat(
    [vertices_uv + u_shift, vertices_uv, vertices_uv + u_shift + v_shift],
    dim=1
)
top_verts_uv = torch.cat(
    [vertices_uv + v_shift, vertices_uv, vertices_uv + u_shift + v_shift],
    dim=1
)
```

- 각 정사각형 셀에 대해 Bottom 삼각형과 Top 삼각형의 정점 UV 좌표는 다음과 같습니다:
  - Bottom 삼각형:
    ```css
    tensor([[[   1,    0], [   0,    0], [   1,    1]],
        [[   2,    0], [   1,    0], [   2,    1]],
        [[   3,    0], [   2,    0], [   3,    1]],
        ...,
        [[ 998,  999], [ 997,  999], [ 998, 1000]],
        [[ 999,  999], [ 998,  999], [ 999, 1000]],
        [[1000,  999], [ 999,  999], [1000, 1000]]], device=rc.device)
    ```
  - Top 삼각형:
    ```css
    tensor([[[   0,    1], [   0,    0], [   1,    1]],
        [[   1,    1], [   1,    0], [   2,    1]],
        [[   2,    1], [   2,    0], [   3,    1]],
        ...,
        [[ 997, 1000], [ 997,  999], [ 998, 1000]],
        [[ 998, 1000], [ 998,  999], [ 999, 1000]],
        [[ 999, 1000], [ 999,  999], [1000, 1000]]], device=rc.device)
    ```
  - 지금의 상황을 그림으로 그려보면 다음과 같이 vertices_uv coordinates를 만들었고, 각 삼각형의 vertices 3개씩을 vertices_uv coordinates에 할당한 것입니다. (아직은 실제 사용하는 UV map처럼 0~1 값으로 normalize까지 한 상태는 아닙니다.)
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b52f79c0-16d7-43f7-9487-4b8bd2a63674)


### 4. UV 좌표 스케일링 및 정규화
- vertices_uv를 텍스처 사이즈에 맞게 스케일링하고, 정규화합니다.

```python
verts_uv = torch.cat([bottom_verts_uv, top_verts_uv], dim=0)
verts_uv = verts_uv * square_size
verts_uv = verts_uv.reshape(-1, 2) / texture_size
```

- `square_size = 10`, `texture_size = 10000`일 때:
  - 정규화된 UV 좌표는 다음과 같이 나옵니다:
    ```css
    tensor([[0.0010, 0.0000],
        [0.0000, 0.0000],
        [0.0010, 0.0010],
        [0.0020, 0.0000],
        [0.0010, 0.0000],
        [0.0020, 0.0010],
        [0.0030, 0.0000],
        [0.0020, 0.0000],
        [0.0030, 0.0010],
        ...,
        [0.9990, 0.9990],
        [0.9980, 0.9990],
        [0.9990, 1.0000],
        [1.0000, 0.9990],
        [0.9990, 0.9990],
        [1.0000, 1.0000]], device=rc.device)
    ```
  - 이 정규화된 UV 좌표에 각 삼각형으로부터 계산한 픽셀 인덱스를 할당하고, 그 후에 각 픽셀이 해당 삼각형 내부에서 가지는 Barycentric Coordinates(무게중심 좌표)를 계산합니다. 이를 통해 local gaussian 불투명도의 합으로 밀도 필드를 계산하고, 렌더링된 픽셀 값을 사용하여 텍스처 픽셀의 값을 설정합니다. 이 과정을 통해 UV 텍스처 이미지를 추출합니다.
  - uv texture image (garden scene)
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7c1cdac8-1e89-4f20-b204-f0e66309318a)

### 5. Texture image을 생성시, 기본적으로, `--square_size`는 texture image에서 mesh의 triangle을 매핑하는 데 사용되는 픽셀 수와 관련이 있습니다.

- 그래서 `--square_size`가 클수록, texture의 resolution이 높아집니다.

Basically, --square_size is related to the number of pixels used to map a triangle of the mesh in the texture image. So the higher square_size, the higher the resolution (and the size) of the texture. Because we optimize the texture on GPU, you can have OOM issues if square_size is too high.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/082a7b92-bde6-4e6e-b0d1-ee9a4a734f52)

[Question about coarse and refined mesh #89](https://github.com/Anttwo/SuGaR/issues/89)

- 즉, 다시 코드를 보면, square_size에 맞게 bottom_triangle_pixel_bary_coords를 결정하는 것을 볼 수 있습니다.
- sqaure_size가 default인 10이라면, triangle의 barycentric coordinates도 10x10의 pixels에서 결정됩니다.
- square_size를 5로 줄이면, triangle의 barycentric coordinates도 5x5의 pixels 내에서 결정되므로 texture image의 해상도가 낮아집니다.
- ***텍스처 해상도는 텍스처 이미지의 크기에 의해 결정되며, Barycentric coordinates는 삼각형 내부의 각 픽셀에 대해 정확한 텍스처 좌표를 할당하는 데 사용됩니다.***

```python
    # ---Build texture image
    # Start by computing pixel indices for each triangle
    texture_img = torch.zeros(texture_size, texture_size, n_features, device=rc.device)    
    pixel_idx_inside_bottom_triangle = torch.zeros(0, 2, dtype=torch.int32, device=rc.device)
    pixel_idx_inside_top_triangle = torch.zeros(0, 2, dtype=torch.int32, device=rc.device)
    for tri_i in range(0, square_size-1):
        for tri_j in range(0, tri_i+1):
            pixel_idx_inside_bottom_triangle = torch.cat(
                [pixel_idx_inside_bottom_triangle, torch.tensor([[tri_i, tri_j]], dtype=torch.int32, device=rc.device)], dim=0)
    for tri_i in range(0, square_size):
        for tri_j in range(tri_i+1, square_size):
            pixel_idx_inside_top_triangle = torch.cat(
                [pixel_idx_inside_top_triangle, torch.tensor([[tri_i, tri_j]], dtype=torch.int32, device=rc.device)], dim=0)
    
    bottom_triangle_pixel_idx = torch.cartesian_prod(
        torch.arange(n_square_per_axis, device=rc.device), 
        torch.arange(n_square_per_axis, device=rc.device))[:, None] * square_size + pixel_idx_inside_bottom_triangle[None]
    top_triangle_pixel_idx = torch.cartesian_prod(
        torch.arange(n_square_per_axis, device=rc.device), 
        torch.arange(n_square_per_axis, device=rc.device))[:, None] * square_size + pixel_idx_inside_top_triangle[None]
    triangle_pixel_idx = torch.cat(
        [bottom_triangle_pixel_idx[:, None], 
        top_triangle_pixel_idx[:, None]],
        dim=1).view(-1, bottom_triangle_pixel_idx.shape[-2], 2)[:n_triangles]
    
    # Then we compute the barycentric coordinates of each pixel inside its corresponding triangle
    bottom_triangle_pixel_bary_coords = pixel_idx_inside_bottom_triangle.clone().float()
    bottom_triangle_pixel_bary_coords[..., 0] = -(bottom_triangle_pixel_bary_coords[..., 0] - (square_size - 2))
    bottom_triangle_pixel_bary_coords[..., 1] = (bottom_triangle_pixel_bary_coords[..., 1] - 1)
    bottom_triangle_pixel_bary_coords = (bottom_triangle_pixel_bary_coords + 0.) / (square_size - 3)
    bottom_triangle_pixel_bary_coords = torch.cat(
        [1. - bottom_triangle_pixel_bary_coords.sum(dim=-1, keepdim=True), bottom_triangle_pixel_bary_coords],
        dim=-1)
    top_triangle_pixel_bary_coords = pixel_idx_inside_top_triangle.clone().float()
    top_triangle_pixel_bary_coords[..., 0] = (top_triangle_pixel_bary_coords[..., 0] - 1)
    top_triangle_pixel_bary_coords[..., 1] = -(top_triangle_pixel_bary_coords[..., 1] - (square_size - 1))
    top_triangle_pixel_bary_coords = (top_triangle_pixel_bary_coords + 0.) / (square_size - 3)
    top_triangle_pixel_bary_coords = torch.cat(
        [1. - top_triangle_pixel_bary_coords.sum(dim=-1, keepdim=True), top_triangle_pixel_bary_coords],
        dim=-1)
    triangle_pixel_bary_coords = torch.cat(
        [bottom_triangle_pixel_bary_coords[None],
        top_triangle_pixel_bary_coords[None]],
        dim=0)  # 2, n_pixels_per_triangle, 3
```

**위 코드를 통해 각 삼각형(top_triangle, bottom_triangle)의 내부 픽셀에 대해 Barycentric coordinates를 계산할 수 있으며, 이를 사용하여 색상이나 텍스처 좌표를 정확하게 보간할 수 있습니다.**

## 다양한 3D 그래픽스 응용 분야에서 Barycentric Coordinates의 사용 예시

Barycentric coordinates는 3D 그래픽스에서 매우 유용하며 다양한 응용 분야에서 사용됩니다. 다음은 그 구체적인 예시들입니다:

### 1. 쉐이딩 (Shading)

Barycentric coordinates는 삼각형의 내부에서 색상이나 법선(normal)을 보간하는 데 사용됩니다. 이를 통해 매끄러운 쉐이딩을 구현할 수 있습니다. 예를 들어, 각 정점(vertex)에 정의된 색상이나 법선을 삼각형의 내부 점으로 보간하여 표면이 평평하지만 매끄러운 색상 변화를 가지도록 할 수 있습니다. 이를 Phong shading이나 Gouraud shading과 같은 기술에서 사용합니다.

### 2. 텍스처 매핑 (Texture Mapping)

**Barycentric coordinates는 삼각형의 각 정점에 정의된 텍스처 좌표를 삼각형 내부의 점들로 보간하는 데 사용됩니다. 이를 통해 삼각형 표면에 텍스처 이미지를 매끄럽게 적용할 수 있습니다. 각 정점에 텍스처 좌표를 할당하고, 삼각형 내부의 각 픽셀에서 해당 텍스처 좌표를 계산하여 정확한 텍스처 색상을 가져올 수 있습니다.**

### 3. 충돌 감지 (Collision Detection)

물체의 충돌을 감지하는 과정에서, Barycentric coordinates는 삼각형 내부의 특정 점이 충돌 지점인지 확인하는 데 사용됩니다. 예를 들어, 물체가 삼각형 표면과 충돌하는 경우, 충돌 지점을 삼각형의 정점 좌표와 Barycentric coordinates를 사용하여 계산할 수 있습니다.

### 4. 애니메이션 (Animation)

Barycentric coordinates는 스킨닝(skinning) 기법에서 사용됩니다. 캐릭터 애니메이션에서 뼈대(bone)와 피부(skin)의 변형을 매끄럽게 연결하기 위해 각 정점의 영향을 여러 뼈대에 걸쳐 보간합니다. 이 과정에서 각 정점이 여러 뼈대의 영향을 받을 때, Barycentric coordinates를 사용하여 각 뼈대의 변형을 정점에 보간하여 적용합니다.

### 5. 조명 계산 (Lighting Calculation)

조명 계산에서도 Barycentric coordinates가 사용됩니다. 각 정점에서의 조명 정보를 삼각형 내부의 점들로 보간하여 매끄러운 조명 변화를 구현할 수 있습니다. 이를 통해 더 현실감 있는 조명 효과를 구현할 수 있습니다.

### 6. 물리 기반 렌더링 (Physically Based Rendering, PBR)

PBR에서 Barycentric coordinates는 정점 속성(vertex attributes)을 보간하는 데 사용됩니다. 각 정점에 정의된 재질 속성(material properties)을 삼각형 내부의 점으로 보간하여 다양한 광원 조건에서도 일관된 렌더링 결과를 제공합니다.

이러한 예시들은 Barycentric coordinates가 3D 그래픽스에서 얼마나 다양한 방식으로 사용되는지를 보여줍니다. 각 기술들은 Barycentric coordinates의 보간 특성을 활용하여 복잡한 그래픽스를 보다 효율적이고 현실감 있게 구현할 수 있습니다.

### Barycentric Coordinates를 이용한 Shading과 Interpolation

[Using Barycentric Coordinates](https://www.scratchapixel.com/lessons/3d-basic-rendering/ray-tracing-rendering-a-triangle/barycentric-coordinates.html)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e0fa5088-b945-42c7-8d3a-5f128019387e)

Figure 3: Barycentric coordinates는 교차점에서 vertex data를 interpolate하는 데 사용할 수 있습니다. 이 예에서는 vertex color를 사용하여 P의 색상을 계산합니다.

Barycentric coordinates는 shading에 매우 유용합니다. 삼각형은 평면이고, 각 vertex에 추가 정보나 데이터를 연관시킬 수 있습니다(점, 색상, 벡터 등). 이러한 정보를 vertex data라고 부릅니다. 예를 들어, vertex A를 빨간색, vertex B를 초록색, vertex C를 파란색으로 하고 싶다고 가정해봅시다.

**교차점이 삼각형의 vertex 중 하나와 일치하면, 교차점에서의 물체 색상은 해당 vertex에 연관된 색상과 동일합니다.** 간단하죠. 문제는 광선이 삼각형의 다른 곳(모서리 또는 내부)과 교차할 때 발생합니다. Barycentric coordinates를 사용하여 삼각형의 vertex를 사용해 점의 위치를 계산하면, 동일한 방식으로 삼각형의 vertex에서 정의된 다른 데이터(예: 색상)를 interpolate 할 수 있습니다.

**즉, Barycentric coordinates는 vertex data를 삼각형의 표면 전체에 걸쳐 interpolate하는 데 사용됩니다**(이 기술은 float, color 등 어떤 데이터 타입에도 적용될 수 있습니다). 이 기술은 예를 들어 교차점에서 normal을 interpolate하기 위해 shading에 매우 유용합니다. 물체의 normal은 face 또는 vertex 기준으로 정의될 수 있습니다(우리는 face normal 또는 vertex normal이라고 합니다).

**vertex 기준으로 정의된 경우, 이 interpolate 기술을 사용하여 삼각형 표면 전체에 걸쳐 매끄러운 shading을 시뮬레이션할 수 있습니다.** 삼각형은 "수학적으로" 평면이지만, 교차점에서의 normal은 vertex normal의 조합이므로, vertex normal이 서로 다르면 이 interpolate의 결과는 삼각형 표면 전체에서 일정하지 않습니다.

**Barycentric coordinates는 texture coordinates를 계산하는 데도 사용됩니다.** 이는 텍스처 매핑(texture mapping)에서 매우 중요합니다. 삼각형의 각 vertex에 텍스처 좌표를 정의하고, Barycentric coordinates를 사용하여 삼각형 내부의 모든 점에 대해 이 텍스처 좌표를 interpolate할 수 있습니다. ***텍스처 해상도는 텍스처 이미지의 크기와 관련이 있으며, Barycentric coordinates를 통해 삼각형 내부의 각 픽셀에 정확한 텍스처 좌표를 할당할 수 있습니다.***

이렇게 Barycentric coordinates는 삼각형 내부의 모든 점에서의 색상이나 텍스처 좌표를 정확하게 계산하는 데 매우 유용합니다.
 
### 요약
- **vertices_uv**: 각 정사각형 셀의 정점 UV 좌표를 생성하여 `(0,0)`에서 `(1,1)` 사이의 값을 가집니다.
- **faces_uv**: 각 삼각형 면의 UV 좌표 인덱스를 생성합니다.
- **UV 좌표 스케일링 및 정규화**: `square_size`와 `texture_size`에 따라 UV 좌표를 스케일링하고 정규화하여 텍스처 이미지의 각 픽셀이 텍스처의 어떤 위치에 대응되는지를 결정합니다.
- **텍스처 이미지 초기화 및 픽셀 인덱스 계산**: 텍스처 이미지를 초기화하고, 각 삼각형의 픽셀 인덱스를 계산합니다.
- **barycentric coordinate 및 density field 생성**: barycentric coordinate를 계산하고, 이를 통해 local gaussian 불투명도의 합으로 밀도 필드를 생성합니다.
- **픽셀 특징 계산 및 텍스처 이미지 업데이트**: barycentric coordinate를 사용하여 각 픽셀의 특징을 계산하고, 이를 기반으로 텍스처 이미지를 업데이트합니다.






































