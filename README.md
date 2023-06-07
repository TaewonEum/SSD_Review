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

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/7ee10996-1b7b-4a5f-85d0-f33d46e5e415)

SSD 모델은 VGG16을 backbone(ImageNet Data Pretrained)으로 사용하고 보조 network(auxiliary network)를 추가한 구조를 가짐

먼저 VGG16을 기반으로 이미지의 특징을 추출한 후, 추가적으로 CNN layer를 더 쌓아서 다양한 크기의 피처맵을 추출함

따라서 다양한 크기의 객체를 탐지할 수 있게 됨

중간 중간 피처맵들에서 모두 Object Detection을 수행함.

*여기서 VGG16의 fully connected layer는 보조 네트워크의 conv layer로 대체되어 학습이 진행됨

전체 네트워크는 CNN을 사용한 이미지 분류와 크게 다를 것이 없어 보임

# 각 단계별 피처맵 Object Detection

VGG layer와 Convolution layer를 통과하는 각각의 Feature map 단계에서

Detector & Classifier를 통과시켜 Object Detection을 수행함.

# Detector & classifier 구조

Localization(탐지)와 Classification(분류) 작업을 수행함

Localization은 Bounding Box위치를 조정하여, 객체의 위치를 더 정확하게 탐지하는 역할을 함.

회귀문제로 취급되며 4개의 값 예측(좌표값을 예측한다는 의미)

Classification에서는 Box가 포함하는 객체의 클래스를 예측함. 즉 클래스 분류임

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/6c5c3f77-c175-4bdd-b821-febd42b91cf3)

위의 이미지는 컨볼루션 중간에 있는 5x5x256 피처맵을 대상으로 Object Detection을 수행하는 과정입니다.

5x5 픽셀마다 각기 다른 디폴트 박스들을 먼저 계산합니다.

디폴트 박스란 쉽게 말해 객체가 있는지 없는 지 예측하는 박스라고 생각하시면 됩니다.

5x5x12인 이유 5x5 피처맵당 3개의 디폴트박스 생성x(좌표값 4개)=>5x5x12

배경 포함 클래스개수가 21개라고 가정한다면 3개의 디폴트 박스마다 21개의 클래스를 분류하기 때문에 5x5x63이 되어

총 75개의 디폴트 박스 생성

# 디폴트 박스 예시

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/ab7efdf2-501c-411d-bd80-2c6b8f047aca)

위 그림을 보면 고양이는 작은 물체이고, 강아지는 상대적으로 더 큰 객체입니다.

피처맵의 해상도가 낮다면 (예: 4x4), 이는 네트워크가 보다 많은 pooling이나 strided convolution을 통과했다는 것을 의미하며, 이는 더 큰 receptive field를 갖는다는 것을 의미합니다. 

이는 네트워크가 이미지의 더 넓은 영역을 한번에 볼수 있다는 것을 의미하며, 이는 큰 객체를 탐지하는 데 유리합니다.

반대로, 피처맵의 해상도가 높다면 (예: 8x8), 이는 네트워크가 더 적은 pooling이나 strided convolution을 통과했다는 것을 의미하며, 이는 더 작은 receptive field를 갖는다는 것을 의미합니다. 

이는 네트워크가 이미지의 더 작은 영역을 한번에 수 있다는 것을 의미하며, 이는 작은 객체를 탐지하는 데 유리합니다.


이해하기 어려우시다면 이미지 상에 해상도가 높은 피처맵에서는 디폴트 박스가 더 작기때문에 작은 객체를 탐지하기 유리하고

해상도가 낮은 피처맵에서는 디폴트 박스가 더 크기 때문에 더 큰 객체를 잘 잡아낸다고 생각하시면 될 것 같습니다.

*디폴트 박스란 Faster R-CNN에서 anchor의 개념으로 비율과 크기가 각기 다른 기본 박스를 먼저 설정해서 Bounding Box를 추론하는데 도움을 주는 장치입니다.

이렇게 각 층별 피처맵들을 가져와 Object Detection을 수행한 결과들을 모두 합하여 Loss를 구하고 전체 네트워크를 학습 시키는 방식으로 Object Detection 모델을 구성합니다

# 디폴트 박스 생성 방법

Object Detection을 수행할 Feature map의 개수=m

피처맵 각각의 Index=k

각 피처맵별 scale level 수식

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/eb658ed6-aa53-45c4-8a82-a739f76f9ed5)

## 예시

s_min=0.2

s_max=0.9

min~max의 구간에서 m값에 따라 적당한 구간을 나눔

m=6일때, 결과는 [0.2, 0.34, 0.48, 0.62, 0.76, 0.9]

해당 값들의 의미는 각각의 피처맵에서 default box의 크기를 계산할 때 입력 이미지 넓이, 높이에 대해 얼만큼 큰지를 나타내는 값임

즉, 첫번째 피처맵에서는 입력 이미지의 0.2 비율을 가진 작은 박스를 default box로 놓겠다는 의미임

각각의 디폴트 박스의 넓이와 높이를 계산

정사각형 뿐만이 아닌, 다양한 비율을 가진 디폴트 박스를 구해야 함

이를 (1,2,3,1/2,1/3) 비율 값을 통해 아래 수식을 계산함

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/4db36669-12db-4f99-93e3-d2f76ff1399c)

예를들어 k=3, 즉 3번째 피처맵의 디폴트 박스들의 width와 height를 구해보면

위에서 구한 scale값을 통해 k=3일때 값은 0.48임(0.2, 0.34, 0.48, 0.62, 0.76, 0.9에서 3번째 scale)

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/99fd58fe-9dae-41a9-967d-011da5f14c62)

5개의 비율에 대한 계산을 진행하고, 추가적으로 현재 스케일 값 보다 조금 더 큰 정사각형 박스를 하나 추가하여 총 6개의 디폴트박스를 생성합니다.

입력 이미지에서 디폴트 박스가 위치할 중심점

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/5b720a5e-8a10-4c22-b1d3-3af05648c2b9)

fk는 피처맵의 가로 세로 크기, 즉 mxn 크기 피처맵의 중심정을 구하는 것

1x1의 피처맵이라면 중심점은 0.5,0.5가 됨

이를 한 칸씩 인덱스를 옮겨가며 계산해주는 작업임

fk가 25라고 한다면 아래와 같은 결과가 나옴

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/5bc0b67e-5327-4c0b-abf2-0f6be1f9172f)

이를 시각화하면 아래와 같음

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/d8c7542a-5e86-4ce1-8d2d-37ed370caf58)

# Loss Function

전체적인 로스의 function은 yolo의 로스 function과 매우 유사함

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/52c8c806-4d8e-4940-964e-3c021b90819d)

전체 로스는 각 클래스 별로 예측한 값과 실제 값의 차이인 Lconf와 바운딩 박스 회귀 예측 값과 실제 값의 차이인 Lloc를 더한 값임

# Lconf

![image](https://github.com/eumtaewon/SSD_Review/assets/104436260/8f7b5d69-0fd2-4bbd-ba50-1ea8b9655d6d)

