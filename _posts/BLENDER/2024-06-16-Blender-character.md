---
title: "[BLENDER] Animation Character"
last_modified_at: 2024-03-27
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - animation
  - blender animation
excerpt: "Blender animation"
use_math: true
classes: wide
---

아래 웹사이트에서 Blender Animation을 위한 rig(character)를 무료로 다운받을 수 있습니다.

Blender Studio Website: https://studio.blender.org/characters/

1. rig를 click하고 `ctrl+Tab`으로 pose mode로 바꿔줘서 animation 작업을 위한 준비를 합니다.

2. `i` (insert keyframe)를 눌러 Location, Rotation & Scaling에 대한 keyframe을 넣어줍니다.

3. Timeline에서 Auto keys를 설정하고 시작합니다.

4. 이제 Timeline을 Dope sheet `ctrl+F12`로 바꿔서 keyframe들을 확인하며 작업하고, 추가적인 modifier로 keyframe의 동작에 효과를 넣고 싶으면 Dope sheet에서 `ctrl+Tab`으로 Graph editor로 바꾸어 작업합니다.

5. rig에서 원하는 부분을 마우스 좌클릭하고, `g` (grab), `r` (rotate), `s` (scale)하여 마우스 좌클릭으로 바꾼 모습을 confirm하거나 우클릭으로 취소합니다.

6. `g`, `r`, `s`할 때, axis를 지정해서 움직일 수 있고, `x`, `y`, `z`를 눌러서 합니다.

7. 마우스 좌클릭으로 여러 개 관절들을 box쳐서 선택할 수 있고, shift + 마우스 좌클릭으로 box를 만들어 빠진 관절도 추가로 선택할 수 있습니다.

8. rig의 한쪽 손가락들을 자연스럽게 pose 시켜놨다면, 그 keyframe들을 `ctrl+c`로 복사한 뒤, 반대 쪽 손가락들에 `ctrl+shift+v`로 mirror한 상태로 붙여넣을 수 있습니다.
