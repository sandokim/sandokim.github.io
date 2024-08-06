---
title: "[3D CV] Project 2D images to point clouds, depth unprojection"
last_modified_at: 2024-08-06
categories:
  - 공부
tags:
  - Multiple View Geometry
  - disparity map
  - inverse depth map
  - depth map
  - depth budget
  - disparity range
  - stereo matching
  - single-image depth estimation
  - metric depth estimation (MDE)
  - relative depth estimation (RED)
  - reprojection
  - unprojection
  - metric depth
  - depth map reprojection
excerpt: "Depth map visualize는 inverse depth로 표현합니다."
use_math: true
classes: wide
comments: true
---

# Unprojection depth map (Project 2D images to point clouds)

[Project 2D images to point clouds github code](https://github.com/DepthAnything/Depth-Anything-V2/blob/main/metric_depth/depth_to_pointcloud.py)

```python
...
    # Process each image file
    for k, filename in enumerate(filenames):
        print(f'Processing {k+1}/{len(filenames)}: {filename}')

        # Load the image
        color_image = Image.open(filename).convert('RGB')
        width, height = color_image.size

        # Read the image using OpenCV
        image = cv2.imread(filename)
        pred = depth_anything.infer_image(image, height)

        # Resize depth prediction to match the original image size
        resized_pred = Image.fromarray(pred).resize((width, height), Image.NEAREST)

        # Generate mesh grid and calculate point cloud coordinates
        x, y = np.meshgrid(np.arange(width), np.arange(height))
        x = (x - width / 2) / args.focal_length_x
        y = (y - height / 2) / args.focal_length_y
        z = np.array(resized_pred)
        points = np.stack((np.multiply(x, z), np.multiply(y, z), z), axis=-1).reshape(-1, 3)
        colors = np.array(color_image).reshape(-1, 3) / 255.0

        # Create the point cloud and save it to the output directory
        pcd = o3d.geometry.PointCloud()
        pcd.points = o3d.utility.Vector3dVector(points)
        pcd.colors = o3d.utility.Vector3dVector(colors)
        o3d.io.write_point_cloud(os.path.join(args.outdir, os.path.splitext(os.path.basename(filename))[0] + ".ply"), pcd)
```

- meshgrid로 x_pixel, y_pixel의 모든 좌표를 구성합니다.

![image](https://github.com/user-attachments/assets/b90c7dc4-6f3e-47fd-8adf-9daa48a932c8)


1. 코드에서 주어진 것처럼 width, height, focal_length_x, focal_length_y의 관계로 명시적으로 써서 x_pixel, y_pixel에서 x_hom, y_hom으로의 변환을 수행해도 되고,

![image](https://github.com/user-attachments/assets/cbffa03b-8cb1-443c-a6a6-e9567db0ecd3)

2. 아래처럼 width/2, height/2, focal_length_x, focal_length_y로 구성된 Camera Intrinsics의 역행렬을 x_pixel, y_pixel에 매트릭스 연산으로 x_hom, y_hom을 구해도 됩니다.

![image](https://github.com/user-attachments/assets/b50127fb-dc11-4a67-9888-5d9757bf3123)

- 최종적으로 [x_hom, y_hom, 1]에 z를 곱하여 3D points를 구합니다.

![image](https://github.com/user-attachments/assets/d10cefc5-a4bf-4f7e-b8db-518c66d2627a)


### Reference
- [Depth Anything V2 for Metric Depth Estimation](https://github.com/DepthAnything/Depth-Anything-V2/tree/main/metric_depth)

