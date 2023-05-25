# SSD_Review

논문: SSD:Single SHot MultiBox Detector

개정 발표:2016.12

single deep neural network를 사용한 객체 탐지 기법으로, SSD라고 불려짐.

1초에 59프레임 처리 가능함

경계 박스 추정과 피처 재추출 단계를 없애 모델 학습 속도를 향상 시킴

입력 이미지의 해상도가 낮더라도 높은 정확도로 객체 탐지가 가능함

# Model

1.Multi scale maps for detection

기본 네트워크 다음 여러 CNN 피처를 덧붙입니다.

CNN layer에서 이미지의 여러 특징들을 추출하여 객체 탐지에 활용합니다.

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/a7fbc8b6-21f3-4213-9bfe-283feb5c5996)

SSD는 여러 차례 합성곱 여산을 해서 다양한 크기의 피처 맵을 만듭니다.

다양한 피처맵마다 픽셀당 가로, 세로 비율이 다른 디폴트 박스가 있습니다.

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/6417e9ec-9efa-4018-82f6-ffb63098cc6e)

각 디폴트 박스마다 경계 박스 좌표와, 경계 박스의 객체 신뢰도 점수(박스에 객체가 존재하는 지 여부를 점수화한 수치)를 예측합니다.

훈련 단계에서는 디폴트 박스를 먼저 ground truth boxes와 매칭함.

위의 이미지 처럼 두 가지 디폴트 박스가 고양이, 강아지에 매칭이 된다면.

이 두 디폴트 박스만을 Positive로 간주하고 나머지 디폴트 박스는 Negative로 간주함




