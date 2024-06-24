---
title: "[Mesh] SuGaR triangle(face), n vertices for gaussian, mesh texturing, TexturesUV, TexturesVertex"
last_modified_at: 2024-06-24
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - Mesh extraction
  - SuGaR
  - coarse mesh
  - refined mesh
  - TriangleMesh
  - read_triangle_mesh
  - open3d
  - o3d
  - closest gaussian
  - compute density
  - triangle에 대한 gaussian density
  - knn
  - barycentric coordiantes
  - barycentric interpolation
  - n_vertices_per_gaussian
  - TexturesUV
  - TexturesVertex
  - mesh texturing
excerpt: "SuGaR triangle(face), n vertices for gaussian, TexturesUV, TexturesVertex"
use_math: true
classes: wide
---

```python
# sugar_scene/sugar_model.py

class SuGaR(nn.Module):

...

        # ---Tools for future meshing---
        # Primitive polygon that will be used to replace the gaussians
        self.primitive_types = primitive_types
        self._diamond_verts = torch.Tensor(
                [[0., -1., 0.], [0., 0, 1.], 
                [0., 1., 0.], [0., 0., -1.]]
                ).to(nerfmodel.device)
        self._square_verts = torch.Tensor(
                [[0., -1., 1.], [0., 1., 1.], 
                [0., 1., -1.], [0., -1., -1.]]
                ).to(nerfmodel.device)
        if primitive_types == 'diamond':
            self.primitive_verts = self._diamond_verts  # Shape (n_vertices_per_gaussian, 3)
        elif primitive_types == 'square':
            self.primitive_verts = self._square_verts  # Shape (n_vertices_per_gaussian, 3)
        self.primitive_triangles = torch.Tensor(
            [[0, 2, 1], [0, 3, 2]]
            ).to(nerfmodel.device).long()  # Shape (n_triangles_per_gaussian, 3)
        self.primitive_border_edges = torch.Tensor(
            [[0, 1], [1, 2], [2, 3], [3, 0]]
            ).to(nerfmodel.device).long()  # Shape (n_edges_per_gaussian, 2)
        self.n_vertices_per_gaussian = len(self.primitive_verts)
        self.n_triangles_per_gaussian = len(self.primitive_triangles)
        self.n_border_edges_per_gaussian = len(self.primitive_border_edges)
        self.triangle_scale = triangle_scale

...

   @property
    def triangle_vertices(self):
        # Apply shift to triangle vertices
        if self.primitive_types == 'diamond':
            self.primitive_verts = self._diamond_verts
        elif self.primitive_types == 'square':
            self.primitive_verts = self._square_verts
        else:
            raise ValueError("Unknown primitive type: {}".format(self.primitive_types))
        triangle_vertices = self.primitive_verts[None]  # Shape: (1, n_vertices_per_gaussian, 3)
        
        # Move canonical, shifted triangles to the local gaussian space
        # We need to permute the scaling axes so that the smallest is the first
        scale_argsort = self.scaling.argsort(dim=-1)
        scale_argsort[..., 1] = (scale_argsort[..., 0] + 1) % 3
        scale_argsort[..., 2] = (scale_argsort[..., 0] + 2) % 3
        
        # TODO: Change for a lighter computation that does not require to compute the rotation matrices.
        # We can just permute the axes of triangle_vertices with the inverse permutation.
        
        # Permute scales
        scale_sort = self.scaling.gather(dim=1, index=scale_argsort)
        
        # Permute rotation axes
        rotation_matrices = quaternion_to_matrix(self.quaternions)
        rotation_sort = rotation_matrices.gather(dim=2, index=scale_argsort[..., None, :].expand(-1, 3, -1))
        quaternion_sort = matrix_to_quaternion(rotation_sort)
        
        triangle_vertices = self.points.unsqueeze(1) + quaternion_apply(
            quaternion_sort.unsqueeze(1),
            triangle_vertices * self.triangle_scale * scale_sort.unsqueeze(1))
        
        triangle_vertices = triangle_vertices.view(-1, 3)  # Shape: (n_pts * n_vertices_per_gaussian, 3)
        return triangle_vertices
    
    @property
    def triangle_border_edges(self):
        edges = self.primitive_border_edges[None]  # Shape: (1, n_border_edges_per_gaussian, 2)
        edges = edges + 4 * torch.arange(len(self.points), device=self.device)[:, None, None]  # Shape: (n_pts, n_border_edges_per_gaussian, 2)
        edges = edges.view(-1, 2)  # Shape: (n_pts * n_border_edges_per_gaussian, 2)
        return edges
    
    @property
    def triangles(self):
        triangles = self.primitive_triangles[None].expand(self.n_points, -1, -1).clone()  # Shape: (n_pts, n_triangles_per_gaussian, 3)
        triangles = triangles + 4 * torch.arange(len(self.points), device=self.device)[:, None, None]  # Shape: (n_pts, n_triangles_per_gaussian, 3)
        triangles = triangles.view(-1, 3)  # Shape: (n_pts * n_triangles_per_gaussian, 3)
        return triangles

```

- 위 코드에서 primitive가 `diamond` 혹은 `square`로 결정되는데, 여기서 두 케이스 모두 정점인 vertices의 개수는 4개입니다.
- len(self.primitive_verts)로 self.n_vertices_per_gaussian으로 설정하는데, 이 말은 gaussian당 vertices를 4개로 정한다는 의미입니다.
- 즉, `Gaussian의 primitive representation`을 `diamond` 혹은 `sqaure`로 정의합니다.
- 그리고 `diamond` 혹은 `sqaure`는 top triangle과 bottom triangle 2개로 구성됩니다.
- 따라서 `diamond` 혹은 `sqaure`에 대한 vertices, edges는 triangle로 모두 계산합니다.
- triangle_vertices를 정의할 때는, None으로 배치차원 1을 앞에 추가해줍니다.
- 즉 triangle_vertices는 1개의 triangle에 대하여 vertices는 4개를 가지고 있고, 각 vertices는 3차원 (x,y,z)를 가지므로 shape은 (1, n_vertices_per_gaussian, 3)에서 결과적으로 (1, 4, 3)이 됩니다.
- 결론적으로 triangle 1개당 local gaussian 1개를 할당하기 위한 작업입니다.

```python
self.n_vertices_per_gaussian = len(self.primitive_verts)
...
triangle_vertices = self.primitive_verts[None]  # Shape: (1, n_vertices_per_gaussian, 3)
```

- triangle당 vertices를 정의했으니, points 수만큼 edges와 triangles을 정의해줍니다.
- `edges` 속성은 Gaussian과 연관된 각 primitive shape의 wireframe 또는 boundary를 정의하는 데 매우 중요합니다. 이 edges는 rendering 목적이나 도형 표면에서 수행해야 하는 모든 geometric computations에 사용할 수 있습니다.
- 요약하자면, `edges` 속성은 Gaussian을 대체하는 모든 primitive shapes의 boundary edges를 포함하는 tensor를 구성하고 반환합니다. 이를 통해 모델은 `각 Gaussian의 primitive representation`에서 vertices 간의 구조와 connections을 추적할 수 있습니다.

```python
    @property
    def triangle_border_edges(self):
        edges = self.primitive_border_edges[None]  # Shape: (1, n_border_edges_per_gaussian, 2)
        edges = edges + 4 * torch.arange(len(self.points), device=self.device)[:, None, None]  # Shape: (n_pts, n_border_edges_per_gaussian, 2)
        edges = edges.view(-1, 2)  # Shape: (n_pts * n_border_edges_per_gaussian, 2)
        return edges
    
    @property
    def triangles(self):
        triangles = self.primitive_triangles[None].expand(self.n_points, -1, -1).clone()  # Shape: (n_pts, n_triangles_per_gaussian, 3)
        triangles = triangles + 4 * torch.arange(len(self.points), device=self.device)[:, None, None]  # Shape: (n_pts, n_triangles_per_gaussian, 3)
        triangles = triangles.view(-1, 3)  # Shape: (n_pts * n_triangles_per_gaussian, 3)
        return triangles
```

# TexturesUV와 TexturesVertex의 차이

**TexturesUV**:
- UV 매핑 기반 텍스처링을 사용합니다.
- 텍스처 맵과 vertices, faces의 UV 좌표를 포함합니다.
- 이는 텍스처 맵을 각 삼각형에 매핑하여 렌더링합니다.
- 주로 전체 이미지 기반 텍스처를 사용하는 경우에 적합합니다.

**TexturesVertex**:
- Vertex 기반 텍스처링을 사용합니다.
- 각 vertex마다 텍스처 색상 특징을 직접 정의합니다.
- 이는 각 vertex에 색상을 지정하여 렌더링합니다.
- 주로 각 vertex에 개별 색상을 지정해야 하는 경우에 적합합니다.

두 방식의 주요 차이는 텍스처가 정의되는 방식과 그에 따른 렌더링 방식입니다. **TexturesUV**는 UV 좌표를 기반으로 텍스처 맵을 매핑하는 반면, **TexturesVertex**는 각 vertex에 직접 색상 정보를 할당합니다.

## Mesh texturing options (Vertex textures, Texture map + Vertex UV coordinates, Texture Atlas)

pytorch3d는 Mesh texturing을 위해 여러 옵션을 제공합니다. 

- **Vertex textures (TexturesVertex)**: 가장 간단한 방법은 각 정점에 대해 d차원 텍스처를 가지는 것입니다. 예를 들어, RGB 색상은 face를 가로질러 보간될 수 있습니다. 이는 N x V x D 텐서로 표현될 수 있습니다.

- **Texture map + Vertex UV coordinates (TexturesUV)**: 두 번째 방법은 정점 UV 좌표와 전체 face에 대한 단일 텍스처 맵을 가지는 것입니다. face의 특정 지점에 대해 색상은 UV 좌표를 보간한 다음 텍스처 맵에서 샘플링하여 계산될 수 있습니다. 이 표현은 두 개의 텐서를 필요로 하며 mesh당 하나의 텍스처 맵만 지원할 수 있습니다.

- **Texture Atlas**: 보다 복잡한 경우, 예를 들어 ShapeNet meshes의 경우, mesh당 여러 개의 텍스처 맵이 있으며 일부 face는 텍스처가 없고 다른 face는 텍스처가 있을 수 있습니다. 이러한 경우, 보다 유연한 표현은 텍스처 아틀라스(texture atlas)입니다. 여기서 각 face는 사용자가 결정한 텍스처 해상도 R에 따른 R x R 텍스처 맵으로 표현됩니다. 이는 소프트 래스터라이저(soft rasterizer) 구현에서 영감을 받았습니다. face의 특정 지점에 대해 텍스처 값은 해당 지점의 중점 좌표(barycentric coordinates)를 사용하여 face의 텍스처 맵에서 샘플링할 수 있습니다. 이 표현은 N x F x R x R x 3 형태의 하나의 텐서를 필요로 합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9cc14647-ab9d-469c-8e7d-fc703cad2b9f)
- [Pytorch3D FAIR Vision 유튜브](https://youtu.be/Sb9gCCnSAUg?si=EhRfZjjZOtM7_d9Q&t=1523)


```python
# sugar_scene/sugar_model.py

class SuGaR(nn.Module):

...

    @property
    def texture_features(self):
        if not self._texture_initialized:
            self.update_texture_features()
        return self.sh_coordinates[self.point_idx_per_pixel]
    
    @property
    def mesh(self):        
        textures_uv = TexturesUV(
            maps=SH2RGB(self.texture_features[..., 0, :][None]), #texture_img[None]),
            verts_uvs=self.verts_uv[None],
            faces_uvs=self.faces_uv[None],
            sampling_mode='nearest',
            )
        
        return Meshes(
            verts=[self.triangle_vertices],   
            faces=[self.triangles],
            textures=textures_uv,
        )
        
    @property
    def surface_mesh(self):
        # Create a Meshes object
        surface_mesh = Meshes(
            verts=[self._points.to(self.device)],   
            faces=[self._surface_mesh_faces.to(self.device)],
            textures=TexturesVertex(verts_features=self._vertex_colors[None].clamp(0, 1).to(self.device)),
            # verts_normals=[verts_normals.to(rc.device)],
            )
        return surface_mesh
```






-----

- SuGaR의 mesh refine에서 사용하는 surface_face에 대해선 barycentric coordinates로 barycentric interpolation을 하는 방식을 사용합니다.
- 즉, triangle의 정점 3개로부터 weights의 조합을 여러개 정의하여 3개 정점에 위치하는 gaussian을 value로 취급하여 barycentric interploation하여 triangle 내부에 gaussian들을 smooth하게 interpolate하여 정의합니다.
- [자세한 내용은 본인이 직접 정의한 barycentric coordinate 관련 포스트 참고](https://github.com/sandokim/sandokim.github.io/blob/master/_posts/%EC%97%B0%EA%B5%AC/2024-06-21-SuGaR-sugar-model-extract-texture-image-and-uv-from-gaussians.md)
