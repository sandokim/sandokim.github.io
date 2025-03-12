---
title: "[MVS] Plane sweep algorithm in multi-view stereo"
last_modified_at: 2025-03-11
categories:
  - MVS
tags:
  - MVS
  - Multi view stereo
  - plane sweep
  - plane sweep algorithm
  - plane homography
  - Lambertian surface
  - MVSNet
  - MVSNeRF
  - MVSGaussians
excerpt: "Plane sweep algorithm in multi-view stereo"
use_math: true
classes: wide
comments: true
---

> Reference

> [A space-sweep approach to true multi-image matching](https://ieeexplore.ieee.org/document/517097)

> [Plane sweep algorithm in multi-view stereo](https://medium.com/@alokguy2004/plane-sweep-algorithm-in-multi-view-stereo-fd070bb0d92f)

> [Plane Sweeping](https://link.springer.com/referenceworkentry/10.1007/978-3-030-63416-2_205)

> [MVSGaussian: Fast Generalizable Gaussian Splatting Reconstruction from Multi-View Stereo](https://arxiv.org/abs/2405.12218)

# Plane Sweep Algorithm in Multi-view Stereo

Plane sweep 알고리즘은 다중 시점 스테레오(Multi-view Stereo)에서 2D 이미지 집합으로부터 3D 장면을 재구성하기 위해 널리 사용되는 기법입니다. 이 알고리즘의 핵심 아이디어는 3D 공간을 따라 하나의 평면을 쓸어가면서, 각 위치에서 장면의 2D 투영을 해당 평면 상에 계산하는 것입니다. 이를 통해 평면의 각 픽셀에 대해 장면의 깊이 정보를 얻을 수 있으며, 이 깊이 정보를 활용하여 전체 3D 장면을 재구성할 수 있습니다.

Plane sweep 알고리즘은 먼저 서로 다른 시점에서 촬영된 장면의 2D 이미지들을 선택하는 것에서 시작합니다. 이후, 장면을 통과하는 하나의 평면을 정의하고, 이 평면을 3D 공간을 따라 이동시키며 각 위치에서 장면의 2D 투영을 평면 상에 얻습니다. 각 위치에서, 평면 위의 2D 투영들 간의 대응 관계를 이용해 삼각 측량(triangulation)을 수행하여 깊이 정보를 계산합니다. 마지막으로, 이렇게 얻어진 깊이 정보를 바탕으로 3D 장면을 재구성하게 됩니다.

Plane sweep 알고리즘은 2D 이미지 집합으로부터 3D 장면을 재구성하는 데 매우 유용한 도구이며, 다른 기법들에 비해 여러 가지 장점을 가지고 있습니다. 예를 들어, plane sweep 알고리즘은 계산 효율성이 높고, 복잡한 기하 구조나 질감을 가진 장면에도 잘 대응할 수 있습니다. 그러나 이 알고리즘은 몇 가지 한계점도 가지고 있는데, 예를 들어 노이즈에 민감하거나 카메라 캘리브레이션의 부정확성에 영향을 받는다는 점이 있습니다.

# Plane Sweep in COLMAP

COLMAP은 이미지로부터 3D 재구성을 수행하는 데 널리 사용되는 오픈 소스 소프트웨어입니다. 이 소프트웨어는 Structure-from-Motion(SfM)과 Multi-View Stereo(MVS) 파이프라인을 기반으로 하며, 이미지 기반 3D 재구성을 위한 종합적인 프레임워크를 제공합니다. COLMAP에서는 다중 시점 스테레오(MVS)에서 깊이 추정을 위해 plane sweep 알고리즘을 사용하며, 이 알고리즘의 성능을 최적화하기 위해 다양한 옵션과 파라미터를 사용자에게 제공합니다.

COLMAP에서 plane sweep 알고리즘은 multi-view stereo(MVS) 파이프라인의 일부로 구현되어 있습니다. 이 파이프라인은 카메라 캘리브레이션, 특징 추출, 매칭, 깊이 추정 등의 여러 단계로 구성됩니다. 이 중 **plane sweep 알고리즘은 깊이 추정 단계에서 사용**되며, 이는 **이미지 상의 2D 투영 정보를 기반으로 장면의 깊이 정보를 계산하는 과정입니다.**

COLMAP의 plane sweep 알고리즘은 3D 공간을 따라 평면을 이동시키면서 각 위치에서 장면의 깊이 정보를 계산하는 방식으로 동작합니다. 이때 **깊이 정보는 평면 위에서의 2D 투영들 간의 대응 관계를 이용한 삼각 측량(triangulation)을 통해 추정**됩니다. COLMAP은 plane sweep 알고리즘의 성능을 조절하기 위해 여러 파라미터들을 제공합니다. 대표적으로는 **sweep할 평면의 개수**, **평면 간의 간격(step size)**, 그리고 **장면의 최대 깊이(maximum depth)** 등이 있으며, 이 파라미터들은 깊이 추정의 정밀도와 계산 효율성에 직접적인 영향을 미칩니다.

COLMAP의 plane sweep 알고리즘의 핵심 특징 중 하나는 복잡한 기하 구조와 질감을 가진 장면도 효과적으로 처리할 수 있다는 점입니다. COLMAP은 모든 이미지 픽셀에 대해 깊이 정보를 계산하는 고밀도 재구성(dense reconstruction) 방식을 사용하기 때문에, 복잡한 구조나 세밀한 텍스처가 있는 장면에서도 정밀한 3D 재구성이 가능합니다. 하지만 이러한 방식은 많은 계산 자원과 메모리를 요구하며, 특히 대규모 장면의 경우 깊이 정보를 계산하는 데 상당한 시간이 소요될 수 있다는 단점도 있습니다.

COLMAP의 plane sweep 알고리즘에서 또 하나의 중요한 특징은 노이즈와 카메라 캘리브레이션 오차에 대한 강인함입니다. COLMAP은 강인한 추정(Robust Estimation) 방식을 적용하여, 캘리브레이션 오류나 이상치(outlier)가 존재하더라도 깊이 추정과 3D 재구성의 정확도를 최대한 유지할 수 있도록 설계되어 있습니다. 이러한 접근 방식은 실제 촬영 환경에서 발생할 수 있는 다양한 불확실성에도 불구하고 정확하고 신뢰할 수 있는 재구성 결과를 얻을 수 있게 해줍니다.

COLMAP에서 plane sweep 알고리즘을 사용하려면, 먼저 여러 시점에서 촬영된 장면의 2D 이미지들을 입력으로 제공해야 합니다. COLMAP은 이 이미지들로부터 자동으로 특징(feature)을 검출 및 추출하고, 서로 대응되는 특징들을 매칭합니다. 그 다음 카메라 캘리브레이션을 추정하고, **추정된 카메라 파라미터를 바탕으로 plane sweep 알고리즘을 통해 깊이 정보를 계산**합니다. 마지막으로, 계산된 깊이 정보를 삼각 측량(triangulation) 하여 최종적으로 3D 장면을 재구성하게 됩니다.

COLMAP의 plane sweep 알고리즘은 이미지로부터 3D 재구성을 수행하는 강력한 도구이며, 컴퓨터 비전과 로보틱스 분야에서 다양한 응용 가능성을 가지고 있습니다. 예를 들어, 자율 주행 내비게이션, 객체 탐지 및 인식, 가상현실(VR) 응용 등에 활용될 수 있습니다. 하지만 이 알고리즘은 정확한 카메라 캘리브레이션이 필요하고, 노이즈에 민감하며, 대규모 장면 처리 시 많은 계산 자원이 요구된다는 한계도 존재합니다.

Plane sweep 알고리즘은 이미지 기반 3D 재구성을 위한 강력한 기술이며, 다른 기술에 비해 여러 가지 장점을 가지고 있습니다. 계산 효율성이 뛰어나고, 복잡한 기하 구조나 질감을 가진 장면도 효과적으로 처리할 수 있습니다. 또한, 노이즈나 카메라 캘리브레이션의 부정확성을 견딜 수 있어 보다 정확한 깊이 추정과 3D 재구성이 가능합니다.

**COLMAP에서는 plane sweep 알고리즘이 multi-view stereo 파이프라인의 일부로 구현되어 있으며**, 알고리즘 성능을 최적화할 수 있도록 다양한 옵션과 파라미터를 제공합니다. COLMAP은 이미지 기반 3D 재구성을 위한 인기 있는 오픈 소스 소프트웨어로, plane sweep 알고리즘을 활용하여 종합적인 3D 재구성 프레임워크를 제공합니다.

전반적으로 plane sweep 알고리즘은 이미지 기반 3D 재구성을 위한 강력한 도구이며, 컴퓨터 비전과 로보틱스 분야에서 다양한 응용 가능성을 가지고 있습니다. 이 기술이 지속적으로 발전함에 따라, 앞으로 plane sweep 알고리즘의 더욱 발전된 응용 사례들이 등장할 것으로 기대됩니다.

**결론적으로, plane sweep 알고리즘은 multi-view stereo에서 다수의 2D 이미지로부터 3D 장면을 재구성하는 강력한 기술**입니다. 복잡한 기하 구조 및 텍스처를 처리할 수 있는 능력과 높은 계산 효율성 덕분에 컴퓨터 비전과 로보틱스 분야에서 널리 사용되고 있습니다. COLMAP은 plane sweep 알고리즘을 기반으로 한 이미지 기반 3D 재구성을 위한 포괄적인 오픈 소스 프레임워크를 제공합니다. 기술이 계속 발전함에 따라, plane sweep 알고리즘의 더욱 정교한 활용이 기대되며, multi-view stereo 분야는 앞으로도 탐색할 가치가 큰 흥미로운 분야입니다.

# 3개 이상의 이미지에서 Plane Sweeping을 사용하는 이유

Binocular stereo에서는 일반적으로 두 이미지를 정렬(rectify)하여, 한 이미지의 시선 방향(ray)을 따라 검색하는 것이 다른 이미지의 한 행(row)을 따라 픽셀을 탐색하는 것과 동일하게 되도록 만듭니다. **하지만 세 개 이상의 이미지에 대해서는 일반적으로 이러한 정렬을 동시에 수행할 수 없습니다.** **Plane sweeping은 3D 공간에서 동작함으로써 이 문제를 회피합니다.** **즉, 이미지 픽셀을 직접 탐색하는 대신, 평면 하나씩 시선 방향을 따라 탐색합니다.**

# Plane Sweeping 이론

- **램버시안 표면(Lambertian surface)은 빛을 모든 방향으로 균일하게 반사하는 표면**을 의미합니다. **즉, 관찰자가 어떤 방향에서 보더라도 밝기가 동일하게 보이는 특성을 가집니다.**

**Plane sweeping은 스테레오 문제를 해결하기 위한 방법으로, 이는 두 개 이상의 캘리브레이션된 뷰가 주어졌을 때 장면의 표면(surfaces)을 찾아내는 문제**입니다. 

- **표면이 램버시안(Lambertian)이라고 가정하고**,
- 가림(occlusion)이 없다고 가정하면,

**하나의 표면 위의 점은 모든 뷰에서 같은 외관(appearance)을 가져야 합니다.** 따라서 스테레오 문제에 대한 개념적인 해결책은 모든 뷰에서 광학적 일관성(photoconsistency)을 최대화하는 점들을 찾아내는 것입니다.

Plane sweep 알고리즘의 입력은 여러 개의 뷰로 구성됩니다. 여기서 뷰(view)란 하나의 이미지와 해당 이미지에 대응하는 카메라 파라미터로 정의됩니다. 간단하게 하기 위해, 여러 뷰 중 하나를 기준(reference) 뷰로 선택합니다. 나머지 모든 이미지는 이 기준 이미지와 비교되어 photoconsistency를 측정하는 데 사용됩니다. 알고리즘의 **출력은 기준 뷰에 대한 깊이 맵(depth map)입니다.**

<img src="https://github.com/user-attachments/assets/7c104ed7-4b6e-4967-a20f-408ee03942ad" width="600">

![image](https://github.com/user-attachments/assets/16fdd5a2-9a64-4068-af0d-c5117335d12e)

<img src="https://github.com/user-attachments/assets/0f7cb9e7-a53e-4752-892d-c8a0acd1f072" width="600">

SSD는 사실상 photoconsistency cost이며, 이는 표면에 가까운 점들에서 최소화됩니다. 참조 이미지(reference image) 공간 내에서 이웃 영역 𝑁을 정의한다는 것은, 이 영역의 점들이 모두 해당 평면 𝜋상에 있다고 가정하는 것입니다. 그러나 기울어진 경사면(slanted surfaces) 이 많은 장면에서는, 다양한 방향(n) 으로 공간을 따라 평면을 쓸어주는 것이 더 나은 결과를 제공합니다. 모든 방향을 탐색하는 것은 느릴 수 있지만, 인공 구조물(man-made scenes) 과 같은 경우에는 몇 개의 지배적인 방향만으로도 충분할 수 있습니다 [1].

Eq. 5는 모든 매칭 뷰가 가려지지 않는다고 가정하지만, 실제로는 가려진 뷰(occluded view) 가 임의로 매우 높은 matching cost를 발생시킬 수 있습니다. Plane sweeping은 여러 뷰를 사용할 수 있으므로, 가려지지 않은 뷰의 부분 집합만을 합산함으로써 occlusion 문제를 완화할 수 있습니다. 예를 들어, 카메라 경로가 선형에 가깝다면, 앞쪽 절반 또는 뒤쪽 절반의 뷰들만 선택해 사용할 수 있습니다. 보다 일반적인 방법은, matching cost가 가장 낮은 상위 50% 뷰만 가려지지 않았다고 가정하는 것입니다 [4].

Plane sweeping 동안, 각 픽셀마다 가장 높은 photoconsistency score를 갖는 평면이 기록됩니다. 이후 각 픽셀의 깊이 값은, 해당 픽셀의 시선 벡터(viewing ray) 와 기록된 최적 평면과의 교차점으로 계산됩니다. Plane sweep 동안 다양한 위치에서 생성된 warped 이미지는 그림 1에 예시되어 있습니다.

![image](https://github.com/user-attachments/assets/ff600df4-e22d-4761-8a2b-f23d8a7ed257)
- **Plane Sweeping, 그림 1**: 합성 장면의 plane sweep 예시. 평면이 전면에서 후면으로 이동하면서, 찻주전자(teapot) 가 먼저 정렬되고, 그 뒤에 있는 매트(mat) 가 정렬되는 것을 볼 수 있습니다.
  
plane sweeping에서는 하나의 평면을 3D 공간에서 앞에서 뒤로 조금씩 이동시키면서, 그 평면 위에 각 카메라 이미지들을 투영(warp) 해 보는 방식으로 깊이 정보를 추정해.

- 예를 들어, 카메라가 어떤 장면을 봤을 때, 그 장면에는 앞쪽에 찻주전자(teapot), 뒤쪽에 매트(mat)가 있다고 해보자.
- 처음 평면이 앞쪽에 있을 때, 찻주전자의 투영이 가장 선명하게 잘 맞아 보이기 때문에, 그 순간 photoconsistency score가 좋아짐 → 이 말은, 그 위치(깊이)가 찻주전자의 실제 위치일 확률이 높다는 뜻.
- 평면을 더 뒤로 이동하면, 이번엔 찻주전자 정렬이 어긋나기 시작하고, 대신 매트의 정렬이 좋아짐 → 매트의 깊이는 더 뒤에 있다는 뜻이야.

_**즉, 평면을 움직이면서 어느 위치에서 어떤 물체가 가장 잘 정렬(=photoconsistent) 되는지를 확인함으로써 그 물체의 깊이(depth)를 추정할 수 있다는 거야.**_

지금까지 이야기에서 언급한 plane 𝜋는 reference frame(참조 뷰 기준 좌표계) 상에 정의된 3D 평면을 의미해.

#### 🔍 왜 reference frame 기준으로 정의할까?
- Plane sweeping 알고리즘에서는 하나의 기준 뷰(reference view) 를 선택하고, 그 뷰에서 각 픽셀에 대응하는 깊이(depth) 를 구하는 것이 목적이야.
- 그래서 plane 𝜋는 기준 카메라 좌표계에서 정의되는 평면이고, 이 평면에 다른 뷰의 이미지들을 warp 해서, 해당 픽셀의 photoconsistency 를 비교하는 구조로 되어 있어.

#### 📐 정리하자면:
- 𝜋는 3D 공간 상에서 정의된 평면이고, reference 카메라의 시선 방향을 기준으로 평면을 앞→뒤로 sweep 한다.
- 이 평면 위에서 reference view의 각 픽셀에 해당하는 3D 점을 계산할 수 있고,
- 다른 카메라 뷰의 이미지를 이 평면 위로 homography를 이용해 warp 해서, 같은 3D 점에 대한 광학적 일관성을 비교하는 거야.

#### 📌 예시 그림을 머릿속에 그려보면:
```markdown
        Camera 1 (reference view)
               |
               |    ← Plane π1 (가까운 깊이)
               |
               |    ← Plane π2 (조금 더 뒤)
               |
               |    ← Plane π3 (더 깊은 곳)
```
이런 식으로 reference camera 앞에 여러 개의 평면이 존재하고, 각각의 평면은 reference 카메라 기준으로 움직이며 photoconsistency를 평가하는 기준이 되는 거지.

Plane sweep에서 3D 공간의 적절한 샘플링은 정확도와 효율성 모두에 매우 중요합니다. 샘플링이 너무 희소하면 photoconsistency의 최적점을 놓칠 수 있고, 반대로 너무 조밀하면 비효율적입니다. Plane sweep 동안 평면은, warped 이미지의 이동량이 1픽셀 이하(또는 원하는 이미지 샘플링 레벨) 가 되도록 움직여야 합니다. 모든 픽셀이 같은 속도로 움직이진 않지만, 참조 이미지의 네 모서리 픽셀만 측정하면 충분합니다. 평면 워핑은 선형이므로, 내부 픽셀들은 모서리 픽셀들의 선형 결합이며 따라서 그 움직임은 모서리 기준으로 제한됩니다.

샘플링 속도를 높이기 위해서는 이미지를 다운샘플링하여 픽셀 크기를 키우는 방식을 사용할 수 있습니다. 또한 해상도 조절 및 뷰 선택(baseline control) 을 통해 샘플링 레이트를 조절할 수 있으며, 이는 정확도와 효율성 간의 최적 균형을 달성하기 위한 방법입니다 [2].

# Plane Sweeping 알고리즘 쉽게 이해하기

<p align="center">
  <img src="https://github.com/user-attachments/assets/ac5bd5a6-86d5-4c31-9957-fcb78beafaf2" alt="Plane Sweep Algorithm Concept"><br>
  <small>&lt;이미지 출처: <a href="https://www.researchgate.net/figure/Concept-of-the-plane-sweep-algorithm_fig4_227221786">https://www.researchgate.net/figure/Concept-of-the-plane-sweep-algorithm_fig4_227221786</a>&gt;</small>
</p>

- Plane Sweeping은 3D 점이 plane 𝜋 위에 있다는 기하학적 제약을 활용하여, 
- reference image의 픽셀을 homogeneous coordinates로 표현하고, 평면까지의 거리 𝑑를 이용해 해당 3D 점의 위치를 계산할 수 있다. 
- 계산된 3D 점은 다른 뷰의 카메라로 projection할 수 있다.

![image](https://github.com/user-attachments/assets/3cc79a21-9e32-406b-8b33-6bbcbef2293d)

- reference 카메라를 기준으로 multiple fornoto-parallel planes at target view (=reference view)를 생성하고
- target view camera의 principal axis의 축을 따라 z 방향을 결정하여, sampled depth $z$를 정합니다. (이때 z는 0~128로 가정)

![image](https://github.com/user-attachments/assets/950696bf-ae88-4da5-8a6c-23e2786c0456)

<img src="https://github.com/user-attachments/assets/3d31eb06-7ee8-4d19-b340-5b3ddd1788f2" width="800">

<img src="https://github.com/user-attachments/assets/9c631f1c-db44-4363-bef7-78ba921dfb97" width="800">

- 알아둘 점: Homography는 두 카메라 사이의 변환이므로 카메라가 바뀌면 homography (H)가 같을 수 없음
