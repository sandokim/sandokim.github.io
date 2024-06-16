---
title: "[BLENDER] Donut tutorial"
last_modified_at: 2024-06-16
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - donut tutorial
excerpt: "Blender donut tutorial"
use_math: true
classes: wide
---

1. `shift+a`로 torus를 선택해 생성하고 default값을 low poly로 생성합니다. (나중에 subdivision을 하여 high res로 만들 것입니다.)

2. 🔧를 눌러 Add modifier에서 Subdivision modifier로 high res으로 만들어줍니다. (Levels Viewport, Render에 같은 값으로 설정해줍니다.)

3. Tab키로 Object mode에서 Edit mode로 바꿔줍니다.

4. Proportional Editing모드로 한번에 동그란 영역에 대해 `g`, `r`, `s`, grab, rotate, scale을 할 수 있고, 마우스 휠로 영향을 미칠 영역의 크기를 조절합니다.

5. `Alt`를 누르고 vertex를 하나 누르면 그 라인에 있는 모든 vertices들이 선택됩니다. `s`를 눌러 scale하고 마우스 왼쪽 클릭으로 confirm 혹은 우클릭으로 취소합니다.

6. `shift+d`로 duplicate하여 선택한 object를 복제할 수 있습니다. 마우스 좌클릭으로 confirm하거나 우클릭으로 취소합니다.

7. Toggle X-ray mode를 선택하면, 마우스 좌클릭으로 box 영역으로 선택할 때 object 뒤쪽까지 한번에 선택이 가능합니다. (gizmo로 x, y, z축에서 선택하면 잘못 vertices를 선택할 일을 방지할 수 있습니다.)


### Tips
- object를 선택한 상태에서 `F2`를 누르면 이름 변경이 가능합니다.
