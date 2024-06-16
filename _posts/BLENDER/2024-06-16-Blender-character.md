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

1. `z`를 눌러 rendering mode (Rendered, Soild, Material Preview, Wireframe 중 하나)를 설정할 수 있습니다. (Material Preview가 일반적입니다.)

1. rig를 click하고 `ctrl+Tab`으로 pose mode로 바꿔줘서 animation 작업을 위한 준비를 합니다.

2. `i` (insert keyframe)를 눌러 Location, Rotation & Scaling에 대한 keyframe을 넣어줍니다.

3. Timeline에서 Auto keys를 설정하고 시작합니다.

4. 이제 Timeline을 Dope sheet `ctrl+F12`로 바꿔서 keyframe들을 확인하며 작업하고, 추가적인 modifier로 keyframe의 동작에 효과를 넣고 싶으면 Dope sheet에서 `ctrl+Tab`으로 Graph editor로 바꾸어 작업합니다.

5. rig에서 원하는 부분을 마우스 좌클릭하고, `g` (grab), `r` (rotate), `s` (scale)하여 마우스 좌클릭으로 바꾼 모습을 confirm하거나 우클릭으로 취소합니다.

6. `g`, `r`, `s`할 때, axis를 지정해서 움직일 수 있고, `x`, `y`, `z`를 눌러서 합니다.

7. 마우스 좌클릭으로 여러 개 관절들을 box쳐서 선택할 수 있고, shift + 마우스 좌클릭으로 box를 만들어 빠진 관절도 추가로 선택할 수 있습니다.

8. rig의 한쪽 손가락들을 자연스럽게 pose 시켜놨다면, 그 keyframe들을 `ctrl+c`로 복사한 뒤, 반대 쪽 손가락들에 `ctrl+shift+v`로 mirror한 상태로 붙여넣을 수 있습니다.

9. IK (Inverse Kinematics), FK (Forward Kinematics): Rotation을 다룰 때는 FK가 자연스럽습니다.

10. `0`를 누르거나 🎥를 눌러서 카메라 뷰로 볼 수 있습니다.

11. `shift+a`로 카메라를 추가해줄 수 있습니다.

12. 카메라에서도 rig를 다룰 때와 같이, Timeline에서 Auto keys를 켜주고, `i`로 Location, Rotation & Scaling인 keyframe을 삽입해줍니다.

13. 일반적으로 사람들이 가장 많이 카메라를 움직이는 방법은, `n`을 누르고 'View` 탭으로 가서 'Camera to View' 옵션을 선택해주면, zoom in & out, rotate를 3D viewport에 맞춰서 해줄 수 있습니다.

14. 카메라를 조절해줬으면 'Camera to View' 옵션을 항상 꺼줍시다.

15. `0`을 눌러서 카메라 렌즈를 통해서 보는 상황에서, shift + ` (tilda)를 눌러 FPS 게임의 1인칭 시점으로서 카메라를 움직일 수 있습니다.
- 이 모드에서 카메라를 움직이는 hotkey는 아래 표시되므로 외울 필요는 없습니다. (W,S,A,D로 상하좌우 이동 E: up, Q: down 등)
- 3D viewport를 하나 더 패널로 열어서 카메라의 3D 위치를 확인하면서 움직이면 좋습니다.

16. motion blur를 방지하기 위해 blender로 camera cuts을 할 때는 여러 대의 카메라를 사용하는게 좋습니다. (real-world에서도 같은 이유로 여러 대의 카메라를 사용합니다.)
- 먼저 Outliner 탭에서 원하는 카메라 🎥를 클릭하여 default camera로 한 뒤에, Marker -> Bind Camera to Markers를 해줍니다.
- 이후 첫 번째 카메라 🎥의 모션이 끝나는 frame 이후에 다음 카메라 🎥를 default camera로 클릭한 상태에서 Marker -> Bind Camera to Markers를 해주면 camera cut이 완성됩니다.

17. Outliner에서 💡를 누르고 Light의 타입을 Point, Sun, Spot, Area로 설정해줄 수 있습니다. 그리고 `shift+t`로 aiming을 할 수 있습니다. Light의 값 위에 마우스를 위치시키고 `i`를 눌러 keyframe을 추가할 수 있습니다.

18. Outliner에서 Render 탭
- Ambient Occlusion은 Light가 미세하게 작은 영역에 대해 약간의 변화를 주어 realistic하게 해줍니다.
- Bloom은 glowing effect를 줍니다.
- Motion Blur를 주면 realistic한 움직임을 줄 수 있습니다.
- Film에서 Transparent를 클릭하면 background를 투명하게 만들어줄 수 있습니다. (compositing할 때 매우 유용한 기능입니다.)
- Simplify를 하면 subdivision level을 줄여 viewport에서 frame rate를 높여줄 수 있습니다. (대신 blocky 해집니다.)


#### Tips
- keyframe을 `i`로 추가할 때, 변화를 주고자 하는 frame 양쪽에 모두 keyframe을 넣고 시작합니다.
- `ctrl+z`로 취소할 수 있고, `ctrl+shift+z`로 취소를 취소할 수도 있습니다.
- `ctrl+s`로 자주 저장해줍시다.
- Timeline, Dope Sheet, Graph Editor에서 `p`를 누르고 마우스 드래그하여 preview range를 설정하여 원하는 시간대의 keyframe들에 대해서만 playback할 수 있습니다.
  - ⏱️를 누르면 preview range가 꺼집니다.
- blender에서는 default 값으로 돌아가고 싶으면 마우스를 값 위에 위치시키고, `backspace`를 누르면 됩니다.
- blender에서는 어떤 properties 위에 마우스를 위치시키고, `i`를 누르면 keyframe이 생깁니다.
  - i.e. 🎥에서 Camera focal, length, Depth of Field등에 대해서도 keyframe 설정이 가능하고, 생성된 keyframe은 Timeline, Dope Sheet, Graph Editor에서도 자동으로 생성됩니다.
- `shift + 마우스 휠 누른상태`로 움직이면 상하좌우로 panning으로 이동합니다.
-  Rendering에서 Sampling -> Viewport Denoising을 off 해줍니다. 


