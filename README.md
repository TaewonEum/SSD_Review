# SSD_Review

논문: SSD:Single SHot MultiBox Detector

개정 발표:2016.12

single deep neural network를 사용한 객체 탐지 기법으로, SSD라고 불려짐.

1초에 59프레임 처리 가능함

경계 박스 추정과 피처 재추출 단계를 없애 모델 학습 속도를 향상 시킴

입력 이미지의 해상도가 낮더라도 높은 정확도로 객체 탐지가 가능함

# Description

SSD는 다양한 view를 활용하면서 통합된 

network 구조를 가진 1-stage detector로 높은 정확도와

빠른 속도를 가진 모델임

# Review

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/3dfb7e47-970b-413c-871e-fa82b85ff843)

SSD 모델은 VGG16을 backbone으로 사용하고 보조 network(auxiliary network)를 추가한 구조를 가짐

먼저 VGG16을 기반으로 이미지의 특징을 추출한 후, 추가적으로 CNN layer를 더 쌓아서 다양한 크기의 피처맵을 추출함

따라서 다양한 크기의 객체를 탐지할 수 있게 됨

*여기서 VGG16의 fully connected layer는 보조 네트워크의 conv layer로 대체되어 학습이 진행됨

전체 네트워크는 CNN을 사용한 이미지 분류와 크게 다를 것이 없어 보임

SSD에서는 다양한 크기의 피처맵을 사용합니다.

SSD의 입력 이미지 크기는 300x300입니다. 

다양한 크기의 피처맵을 사용한다고 하였는데 총 6개의 크기가 다른 feature map을 추출합니다.

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/aba784b6-94ac-4254-a12c-0f6298ddeb39)

다양한 scale의 feature map을 사용하여 다양한 크기의 객체를 탐지하는 것이 가능함.

# Default Boxes

원본 이미지에서 보다 다양한 크기의 객체 탐지를 위해 피처맵의 각 cell마다 서로 다른 스케일과 가로, 세로 비율을 가진 Default box를 생성함

즉, 위의 이미지 처럼 38x38, 19x19, 10x10, 5x5, 3x3, 1x1 총 6개의 scale의 feature map의 각 cell마다 default box를 생성함

Default box의 scale은 원본 이미지에 대한 비율을 의미함

# scale 공식

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/fbf13414-5d35-4197-8f95-c6410a6ababd)

첫 피처맵의 scale은 0.34

마지막 피쳐맵의 scale은 0.9입니다

피처맵의 scale이 작아질수록 디폴트 박스의 스케일은 커집니다.

즉 크기가 작아질수록 작은 피처 맵에서는 입력 이미지의 큰 영역이 

해당 피처 맵의 각 위치에 매핑되므로, 큰 물체를 감지하는 데 가장 적합하다는 논리입니다.





