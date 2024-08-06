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



### Reference
- [Depth Anything V2 for Metric Depth Estimation](https://github.com/DepthAnything/Depth-Anything-V2/tree/main/metric_depth)

