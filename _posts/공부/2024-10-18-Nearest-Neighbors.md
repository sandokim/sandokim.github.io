---
title: "[3D CV] Nearest Neighbors (Ball Tree, KD Tree, Brute force)"
last_modified_at: 2024-10-18
categories:
  - 공부
tags:
  - Nearest Neighbors
  - KNN
  - Brute force
  - KD Tree
  - Ball Tre
excerpt: "Nearest Neighbors (Brute force, KD Tree, Ball Tree) 정리"
use_math: true
classes: wide
comments: true
---

### 1. Brute Force
- Core Concept: Every query point is compared to every point in the dataset.
- Advantages: Guarantees finding the exact nearest neighbors. Simple to implement.
- Disadvantages: Computationally expensive, with a time complexity of O(n) per query, which becomes inefficient for large datasets.
- Use Case: Best for small datasets or situations where exact solutions are critical.

### 2. KD Tree (K-Dimensional Tree)
- Core Concept: A binary tree structure that recursively divides data points into k-dimensional space, based on axis-aligned hyperplanes.
- Advantages: Efficient for low-dimensional data with time complexity of O(log n) for query operations in balanced trees.
- Disadvantages: Performance degrades as dimensionality increases due to the curse of dimensionality. May not handle very high-dimensional data well.
- Use Case: Suitable for medium-sized datasets and low-dimensional spaces.

### 3. Ball Tree
- Core Concept: A tree structure that recursively partitions data points into nested hyperspheres (balls) rather than axis-aligned cuts.
- Advantages: **Performs better in higher-dimensional spaces compared to KD Tree, with more balanced partitioning based on distance metrics.**
- Disadvantages: More complex to implement than KD Tree. Still struggles with very high-dimensional data but handles it better than KD Tree.
- Use Case: Preferred for high-dimensional datasets where KD Tree performance diminishes.
