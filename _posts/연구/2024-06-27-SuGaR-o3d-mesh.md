![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/bc227780-eb14-4942-acfa-cf277d1aec86)---
title: "[3D CV 연구] 3DGS SuGaR surface_mesh_to_bind = o3d_mesh"
last_modified_at: 2024-06-26
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - surface_mesh_to_bind
  - o3d mesh
  - open3d mesh
excerpt: "o3d_mesh의 "
use_math: true
classes: wide
---

### open3d mesh를 불러와서 initialize하는 법을 알아봅시다.

SuGaR에서 사용하는 o3d_mesh의 properties는 다음과 같습니다.

- o3d_mesh.triangles
- o3d_mesh.vertices
- o3d_mesh.vertex_normals
- o3d_mesh.vertex_colors

### SuGaR에서 o3d_mesh로 사용하는 coarse_mesh를 불러와서 그 값들을 확인해봅시다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1d240062-091f-4373-b5b6-ecc60e3d12ce)

```python
import open3d as o3d
import pandas as pd
import numpy as np
import os

# Load the mesh
mesh_path = "C:/Users/maila/Desktop/coarse_mesh/bicycle/sugarmesh_3Dgs7000_sdfestim02_sdfnorm02_level03_decim1000000.ply"
mesh = o3d.io.read_triangle_mesh(mesh_path)

# Extract properties
triangles = np.asarray(mesh.triangles)
vertices = np.asarray(mesh.vertices)
vertex_normals = np.asarray(mesh.vertex_normals)
vertex_colors = np.asarray(mesh.vertex_colors)

# Analyze shapes before slicing
shapes = {
    'Triangles': triangles.shape,
    'Vertices': vertices.shape,
    'Vertex Normals': vertex_normals.shape,
    'Vertex Colors': vertex_colors.shape
}

# Slice first 10 entries
triangles = triangles[:10]
vertices = vertices[:10]
vertex_normals = vertex_normals[:10]
vertex_colors = vertex_colors[:10]

# Check if vertex colors are always positive
all_colors_positive = (vertex_colors >= 0).all()

# Convert to DataFrames
triangles_df = pd.DataFrame(triangles, columns=['v0', 'v1', 'v2'])
vertices_df = pd.DataFrame(vertices, columns=['x', 'y', 'z'])
vertex_normals_df = pd.DataFrame(vertex_normals, columns=['nx', 'ny', 'nz'])
vertex_colors_df = pd.DataFrame(vertex_colors, columns=['r', 'g', 'b'])

# Create a DataFrame to save the shapes
df_shapes = pd.DataFrame({
    'Property': shapes.keys(),
    'Shape': [str(shape) for shape in shapes.values()]
})

# Save shapes DataFrame to CSV
output_shapes_path = os.path.join(os.path.dirname(mesh_path), "mesh_properties_shapes.csv")
df_shapes.to_csv(output_shapes_path, index=False)

# Save the DataFrames to separate CSV files
output_values_paths = {
    'Triangles': os.path.join(os.path.dirname(mesh_path), "mesh_triangles_first_10.csv"),
    'Vertices': os.path.join(os.path.dirname(mesh_path), "mesh_vertices_first_10.csv"),
    'Vertex Normals': os.path.join(os.path.dirname(mesh_path), "mesh_vertex_normals_first_10.csv"),
    'Vertex Colors': os.path.join(os.path.dirname(mesh_path), "mesh_vertex_colors_first_10.csv")
}

triangles_df.to_csv(output_values_paths['Triangles'], index=False)
vertices_df.to_csv(output_values_paths['Vertices'], index=False)
vertex_normals_df.to_csv(output_values_paths['Vertex Normals'], index=False)
vertex_colors_df.to_csv(output_values_paths['Vertex Colors'], index=False)

# Display the shapes DataFrame
print("Mesh Properties Shapes:")
print(df_shapes)

# Display the paths to the saved files
output_paths = {
    'Shapes CSV': output_shapes_path,
    **output_values_paths
}
print("\nSaved Files Paths:")
for key, path in output_paths.items():
    print(f"{key}: {path}")

# Display the result of the vertex colors positivity check
if all_colors_positive:
    print("All vertex colors are positive.")
else:
    print("There are negative values in vertex colors.")
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3fdb5620-5cd0-4ab2-acbe-0848ef80f4a1)

- Shape: o3d_mesh.triangles, o3d_mesh.vertices, o3d_mesh.vertex_normals, o3d_mesh.vertex_colors
- vertices, vertex_normals, vertex_colors의 shape은 같아야 합니다.
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8bca6098-a1d9-4c84-a5a6-3cfd846af30a)

- 첫 10개의 triangles (v0, v1, v2는 x,y,z를 갖는 vertices의 인덱스)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d3706c88-7f13-4927-b30b-74342714d3ee)

- 첫 10개의 vertices
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/48c37ec0-47f1-4247-9627-bd72b87c3668)

- 첫 10개의 vertex_normals
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f21648e3-5676-44b2-abc2-033dec9d55fc)

- 첫 10개의 vertex_colors (항상 양수임)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ac0121aa-f14f-496e-b29d-0a0d90b3e357)



```python
# SuGaR/sugar_scene/sugar_model.py

class SuGaR(nn.Module):
    """Main class for SuGaR models.
    Because SuGaR optimization starts with first optimizing a vanilla Gaussian Splatting model for 7k iterations,
    we built this class as a wrapper of a vanilla Gaussian Splatting model.
    Consequently, a corresponding Gaussian Splatting model trained for 7k iterations must be provided.
    However, this wrapper implementation may not be the most optimal one for memory usage, so we might change it in the future.
    """
    def __init__(
        self, 
        ...
        surface_mesh_to_bind=None,  # Open3D mesh
        ...
        *args, **kwargs) -> None:
        """
        Args:
            ...
            primitive_types (str, optional): Type of primitive to use to replace the Gaussians. Defaults to 'diamond'.
            surface_mesh_to_bind (None, optional): Surface mesh to bind the Gaussians to. Defaults to None.
            surface_mesh_thickness (None, optional): Thickness of the bound Gaussians. Defaults to None.
            learn_surface_mesh_positions (bool, optional): Whether to learn the positions of the bound Gaussians. Defaults to True.
            learn_surface_mesh_opacity (bool, optional): Whether to learn the opacity of the bound Gaussians. Defaults to True.
            learn_surface_mesh_scales (bool, optional): Whether to learn the scales of the bound Gaussians. Defaults to True.
            n_gaussians_per_surface_triangle (int, optional): Number of bound Gaussians per surface triangle. Defaults to 6.
        """

...

        if surface_mesh_to_bind is not None:
            self.learn_surface_mesh_positions = learn_surface_mesh_positions
            self.binded_to_surface_mesh = True
            self.learn_surface_mesh_opacity = learn_surface_mesh_opacity
            self.learn_surface_mesh_scales = learn_surface_mesh_scales
            self.n_gaussians_per_surface_triangle = n_gaussians_per_surface_triangle
            self.editable = editable
            
            self.learn_positions = self.learn_surface_mesh_positions
            self.learn_scales = self.learn_surface_mesh_scales
            self.learn_quaternions = self.learn_surface_mesh_scales
            self.learn_opacities = self.learn_surface_mesh_opacity
            
            self._surface_mesh_faces = torch.nn.Parameter(
                torch.tensor(np.array(surface_mesh_to_bind.triangles)).to(nerfmodel.device), 
                requires_grad=False).to(nerfmodel.device)

...

            points = torch.tensor(np.array(surface_mesh_to_bind.vertices)).float().to(nerfmodel.device)
            # verts_normals = torch.tensor(np.array(surface_mesh_to_bind.vertex_normals)).float().to(nerfmodel.device)
            self._vertex_colors = torch.tensor(np.array(surface_mesh_to_bind.vertex_colors)).float().to(nerfmodel.device)
            faces_colors = self._vertex_colors[self._surface_mesh_faces]  # n_faces, 3, n_coords
            colors = faces_colors[:, None] * self.surface_triangle_bary_coords[None]  # n_faces, n_gaussians_per_face, 3, n_colors
            colors = colors.sum(dim=-2)  # n_faces, n_gaussians_per_face, n_colors
            colors = colors.reshape(-1, 3)  # n_faces * n_gaussians_per_face, n_colors
                
            self._points = nn.Parameter(points, requires_grad=self.learn_positions).to(nerfmodel.device)
            n_points = len(np.array(surface_mesh_to_bind.triangles)) * n_gaussians_per_surface_triangle
            self._n_points = n_points

...

```

- `surface_mesh_to_bind=o3d_mesh`, open3d mesh를 사용합니다.

```python
# SuGaR/sugar_scene/sugar_model.py

def load_refined_model(refined_sugar_path, nerfmodel:GaussianSplattingWrapper):
    checkpoint = torch.load(refined_sugar_path, map_location=nerfmodel.device)
    n_faces = checkpoint['state_dict']['_surface_mesh_faces'].shape[0]
    n_gaussians = checkpoint['state_dict']['_scales'].shape[0]
    n_gaussians_per_surface_triangle = n_gaussians // n_faces

    print("Loading refined model...")
    print(f'{n_faces} faces detected.')
    print(f'{n_gaussians} gaussians detected.')
    print(f'{n_gaussians_per_surface_triangle} gaussians per surface triangle detected.')

    with torch.no_grad():
        o3d_mesh = o3d.geometry.TriangleMesh()
        o3d_mesh.vertices = o3d.utility.Vector3dVector(checkpoint['state_dict']['_points'].cpu().numpy())
        o3d_mesh.triangles = o3d.utility.Vector3iVector(checkpoint['state_dict']['_surface_mesh_faces'].cpu().numpy())
        # o3d_mesh.vertex_normals = o3d.utility.Vector3dVector(normals.cpu().numpy())
        o3d_mesh.vertex_colors = o3d.utility.Vector3dVector(torch.ones_like(checkpoint['state_dict']['_points']).cpu().numpy())
        
    refined_sugar = SuGaR(
        nerfmodel=nerfmodel,
        points=checkpoint['state_dict']['_points'],
        colors=SH2RGB(checkpoint['state_dict']['_sh_coordinates_dc'][:, 0, :]),
        initialize=False,
        sh_levels=nerfmodel.gaussians.active_sh_degree+1,
        keep_track_of_knn=False,
        knn_to_track=0,
        beta_mode='average',
        surface_mesh_to_bind=o3d_mesh,
        n_gaussians_per_surface_triangle=n_gaussians_per_surface_triangle,
        )
    refined_sugar.load_state_dict(checkpoint['state_dict'])
    
    return refined_sugar

```


