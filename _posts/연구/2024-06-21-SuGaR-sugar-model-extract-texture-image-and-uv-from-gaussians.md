---
title: "[3D CV 연구] 3DGS SuGaR sugar_model.py extract_texture_image_and_uv_from_gaussians"
last_modified_at: 2024-06-14
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
excerpt: "3DGS SuGaR sugar_model.py extract_texture_image_and_uv_from_gaussians"
use_math: true
classes: wide
---

### UV map에 texture image를 대응시킬 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d4f5b04b-77e1-44a3-8a21-de2e313691cd)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5abc4d7f-60f7-4312-a04d-1711f9f158de)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f9719d96-b64e-4dc1-9f7f-e6b0015f092b)

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
- 결론부터 말하면, refined_mesh의 faces는 1,997,996개 vertices는 1,025,032개인걸 감안해서 default sqaure_size가 10일 때, uv map 크기가 10000x10000로 나온 것인데 어떻게 계산된 것인지 이해해봅시다.

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






