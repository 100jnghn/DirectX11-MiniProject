# Filter Program
- DirectX11을 사용해 제작한 filter 프로그램입니다.
 <br>

#### ⬇️ Click IMG to play with youtube ⬇️
[![Filter](http://img.youtube.com/vi/1S0xK0OHBn4/0.jpg)](https://youtu.be/1S0xK0OHBn4?t=0s)
<br>
<br>
<br>

## 사용 기술
- DirectX 11
- C++
- hlsl (Shaer Files)
<br>
<br>

## 프로젝트 개요
 일부 게임에서는 물체에 일정 시간 열을 가하거나 물과 닿으면 물체가 변하게 되는 기능을 사용합니다. 대표적인 예시로 마인크래프트에서는 Cube로 된 흙 Object를 화로에서 일정 시간 열을 가하면 돌 Object로 변하게 됩니다. 이렇게 물체에 열이 가해지는 현상을 숫자나 게이지가 점점 차오르는 UI로 표현하지 않고 Object의 Texture에 변화를 줌으로 직관적으로 기능을 관찰할 수 있도록 프로젝트를 구성했습니다.

 이 프로젝트에서는 물체에 Ice Filter와 Fire Filter 기능을 적용하고,<br>
필터에 따른 물체의 색상 변화를 Render To Texture를 통해 관찰할 수 있습니다.
<br>
<br>
<br>

## 기능 소개
- 방향키 ← →를 눌러 Cube 모델을 좌우로 이동시킬 수 있습니다.
- 숫자 1을 눌러 Cube의 앞에 Ice 필터를 띄울 수 있습니다.
- 숫자 2를 눌러 Cube의 앞에 Fire 필터를 띄울 수 있습니다.
- 숫자 0을 눌러 Cube에 가해지는 필터를 제거할 수 있습니다.
- 필터1이나 필터2가 켜진 상태에서 방향키 ↑↓를 눌러 Filter 효과를 키우거나 줄일 수 있습니다.
- Cube 모델의 위치가 필터에서 크게 벗어나면 필터 효과가 가해지지 않습니다.
- 우측 상단 Render Texture를 통해 모델에 가해지는 Filter 효과를 직관적으로 관찰할 수 있습니다.

## 구현 기능 상세
### (1) - Cube Model Render
![image](https://github.com/user-attachments/assets/bc999eaf-cbb4-48e7-93c1-d05524ed5df0)<br>
cube.txt File를 사용해 네모난 큐브 모델을 로드했습니다.<br>
큐브 모델의 Texture는 block.jpg 리소스를 저장해 프로젝트에 포함했고,<br>
normal map 변환 사이트를 이용해 blocknormal.jpg를 만들어 프로젝트에 포함시켰습니다. 아래는 원본 텍스쳐 이미지입니다.<br>
<br>
(좌) block.jpg  /  (우) blockNormal.jpg<br>
<img src="https://github.com/user-attachments/assets/be4bee62-9199-4367-9832-d017f9272b5e" width="400" height="400"/>
<img src="https://github.com/user-attachments/assets/2d11a242-0696-4064-9400-1bc37712865f" width="400" height="400"/>
<br>
<br>
<br>
### (2) - Cube Normal Texture 적용
가져온 두 이미지를 사용해 PixelShader를 작성했습니다.<br>
blockNormal과 bumpMap을 샘플링을 통해 texture의 Value를 가져온 후 bumpMap의 normal 계산을 위해 값의 범위를 –1~1로 변환합니다.<br> 
이 데이터를 통해 bumpMap의 최종 normal 값을 계산합니다.
<br>
<br>
<br>
### (03) - 사용자 입력을 통한 Cube 모델 이동
방향키 ← → 버튼을 눌러서 모델을 좌우로 이동시킬 수 있습니다.<br>
applicationclass에 m_cubePosX 변수를 선언하고 inputclass에 좌우 방향키가 눌렸다면 true를 반환하는<br>
IsLeftArrowPressed(), IsRightArrowPressed() 함수를 작성했습니다.<br>
applicationclass에서 해당 함수의 true 값을 받으면 m_cubePosX의 값을 증가 감소시키고,<br>
이 값을 Render() 함수에 전달하여 worldMatrix의 x값을 이동시켜 모델 움직임을 구현했습니다.<br>
(좌) 왼쪽으로 이동 / (우) 오른쪽으로 이동<br>
<img src="https://github.com/user-attachments/assets/4d2f43e2-d12e-4619-a373-c8bcd5e612b1" width="450" height="300"/>
<img src="https://github.com/user-attachments/assets/6ece9366-4b8e-41b5-acb0-cb16f1652e7f" width="450" height="300"/>
<br>
<br>
<br>
### (04) - Cube에 가해지는 두 개의 조명 구현
Cube 모델에 두 개의 조명이 비춰지도록 m_Light1과 m_Light2를 선언 후 초기화했습니다.<br>
Light1은 붉은색을 띄고 ↙방향을 비추도록 Direction을 설정했습니다.<br>
Light2는 푸른색을 띄고 ↘방향을 비추도록 Direction을 설정했습니다.<br>
<br>
두 조명은 Cube에 적용된 shader인 m_NormalMapShader를 통해 normalPS의 상수 버퍼로 전달되며<br>
전달된 상수 버퍼 값을 사용해 Pixel의 최종 색상을 결정합니다.<br>
<br>
아래는 Cube가 왼쪽으로 이동하여 우측 면에 붉은 조명이 보이고<br>
Cube가 오른쪽으로 이동하여 좌측면에 푸른 조명이 보이는 이미지입니다.<br>
중간 부분은 두 조명이 합쳐져 보라색 색상이 나타납니다.<br>
<img src="https://github.com/user-attachments/assets/498b9e0c-34e8-4e50-bc95-996974031a6c" width="450" height="300"/>
<img src="https://github.com/user-attachments/assets/cda26487-b3fa-4273-b8cd-517c0ffd6f15" width="450" height="300"/>
