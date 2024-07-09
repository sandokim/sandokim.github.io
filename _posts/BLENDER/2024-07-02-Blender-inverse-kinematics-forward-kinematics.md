---
title: "[BLENDER] Inverse Kinematics (IK) & Forward Kinematics (FK)"
last_modified_at: 2024-07-02
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - inverse kinematics
  - ik
  - forward kinematics
  - fk
  - skinning
  - human mesh
excerpt: "fk는 parent bone이 child bone에 영향을, ik는 child bone이 parent bone에 역으로 영향을 미칩니다."
use_math: true
classes: wide
comments: true
---

## Inverse Kinematics (IK)

- IK 예시
  - 아래팔이 움직이면 윗팔의 관절이 같이 움직입니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/56c2dd85-4858-4a04-860b-4c78f460a448)


## Forward Kinematics (FK)

현재 Bone에서의 transfromation이 Bone chain에서 오직 아래에 존재하는 Bone에만 영향을 미치므로 Forward Kinematics라 합니다.

- FK 예시
  - 꼬리의 윗부분이 움직이면 꼬리의 아랫부분까지 같이 움직입니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a3b661dc-ae7e-4c81-89e2-e82e5f7cfebb)

## skinning

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b95a1af7-1a05-4a32-afc9-a97eb1c36ff1)

Blender와 같은 3D 모델링 소프트웨어에서 사용하는 "skinning"이라는 용어는 캐릭터 애니메이션을 위한 필수 개념 중 하나입니다. Skinning은 주로 뼈대(armature)와 메쉬(mesh)를 결합하여 뼈대의 움직임에 따라 메쉬가 변형되는 과정을 의미합니다. 이 과정을 통해 캐릭터의 움직임을 보다 자연스럽게 만들 수 있습니다.

Blender에서의 주요 skinning 방식은 다음과 같습니다:

1. **Forward Kinematics (FK)**: Forward Kinematics는 뼈대를 순차적으로 움직여서 메쉬를 변형시키는 방법입니다. 예를 들어, 팔의 경우 어깨, 팔꿈치, 손목 순서로 각 관절을 수동으로 조작하여 원하는 위치로 이동시킵니다. 이 방식은 직관적이지만 복잡한 움직임을 만들 때 시간이 많이 걸릴 수 있습니다.

2. **Inverse Kinematics (IK)**: Inverse Kinematics는 끝부분(예: 손이나 발)을 원하는 위치로 이동시키면 자동으로 나머지 관절의 위치가 결정되는 방식입니다. 예를 들어, 손을 특정 지점으로 이동시키면 어깨와 팔꿈치의 위치가 자동으로 조정되어 자연스러운 움직임이 생성됩니다. IK는 복잡한 움직임을 만들 때 매우 유용합니다.

**Skinning과 Kinematics의 관계**에 대해 설명하자면:

- `Forward Kinematics (FK)`와 `Forward Skinning`은 동일한 개념을 가리키는 것이 아닙니다. FK는 뼈대의 움직임을 제어하는 방법 중 하나이며, Forward Skinning이라는 용어는 사용되지 않습니다.
  
- `Inverse Kinematics (IK)`와 `Inverse Skinning`도 동일한 개념을 가리키지 않습니다. IK는 특정 지점을 이동시키면 나머지 관절이 자동으로 조정되는 방식입니다. Inverse Skinning이라는 용어도 사용되지 않습니다.

결론적으로, Forward Kinematics = Forward Skinning, Inverse Kinematics = Inverse Skinning 이라는 등식은 성립하지 않습니다.

- **Kinematics는 뼈대의 움직임을 제어하는 방식이고**
- **Skinning은 뼈대와 메쉬를 결합하는 과정입니다**.


### Reference
- [Inverse Kinematics - Blender 2.80 Fundamentals](https://youtu.be/S-2v_CKmVE8?si=OAjBisfIKsLhEnM_)
- [Blender 4.1 Manual](https://docs.blender.org/manual/en/latest/animation/armatures/skinning/introduction.html)
