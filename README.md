### 프로젝트 정리


이번 주 내용 넣을 꺼(빛 번짐 제거하는 데 쓸 cnn기법에 대한 소개와 

cnn이 어떻게 돌아가는지에 대한 설명한 후 이렇게 진행할 꺼다)


발표 내용 6월 까지 이렇게 진행할 까 해요(아직 발표자료에는 개시 ㄴㄴ)

### 3월 말

빛 번짐을 제거를 위한 조사한 기법에 대해서 설명(기본 적인 cnn에 대해서)

### 4월

1주차에 모델 하나 가지고 결과 낼꺼고

2주차에는 모델 다른거 써서 비교한 결과 낼예정

3주차에는 데이터(현재 662)인데 더 늘리는 방법있고 이거에 대한 결과 반영

4주차에는 총 결과 정리

### 5월

1주차에는 밤낮 전환에 필요한 기법 조사

2주차에는 밤낮 바꾸기 1차 구현

3주차에는 임의적으로 빛이 많이 번져 있는 비온날에 대한 데이터로 실험

4주차에는 2,3차 내용 보안

5주차에는 총 정리

### 6월은 지금 까지 내용 총 정리





### model
![이미지](https://user-images.githubusercontent.com/94822037/159855713-7a366f31-bd8e-481b-bc76-d5196295a788.png)

autoencoder 모델은 입력 데이터(input data)를 압축시켜 압축시킨 데이터로 축소한 후 다시 확장하여 결과 데이터를 입력 데이터와 동일하도록 한드는 일종의 딥 뉴럴 네트워크

이 모델은 자기 자신을 결과로 나오게 만들지만 우리는 모델 마지막에 sigmoid를 추가 즉 autoencoding을 통해 이미지의 특징을 추출하고 output이 나오도록 한다.

###   unet++

![unet++](https://user-images.githubusercontent.com/94822037/159856694-1c1e489c-9841-4ca3-abd0-4687f8dca587.PNG)


최종 사용하는 모델의 모형은 다음과 같다.
backbone부분에는 전이 학습 모델을 가져와서 이미지를 축소 시킨다(x0.0~x4.0) 이 후 다시 이미지를 확장 시킨다(x4.0~x0.4)

unet에서 res path부분에도 convolution 연산을 수행 시키는 모델이 unet++이다 결과적이로 x0.1~x0.4의 평균 값 혹은 x0.4의 값을 결과 값으로 사용한다.

### data

https://dacon.io/competitions/official/235746/data

사용한 데이터를 

### result

![첫 결과](https://user-images.githubusercontent.com/94822037/159857508-95429dee-2ff8-441a-aa96-f5a8ee1f802a.PNG)

왼쪽 부터 input image , label image , output image이다.

현재는 DACON에서 개최하는 LG의 data를 가지고 빛번짐 성능을 test 해보았다.

이제 실제 목적인 블랙 박스상 가로등 , 달빛등의 빛번짐을 제거하려고 한다.

### 밤 <-> 낮 전환 인공지능

### Data : https://www.kaggle.com/datasets/solesensei/solesensei_bdd100k

![밤낮 모델](https://user-images.githubusercontent.com/94822037/160829261-90f2f89f-985e-4ea6-b9cd-4421d19d456d.jpg)

GAB 은 밤 사진(RealA)을 낮 사진(FakeB)으로 만드는 모델

GBA 은 낮 사진(RealB)을 밤 사진(FakeA)으로 만드는 모델

DA 는 밤 사진(FakeA)와 밤 사진(RealA) 간의 차이를 줄이는 모델

DB 는 낮 사진(FakeB)와 낮 사진(RealA) 간의 차이를 줄이는 모델

### generator


![GAB](https://user-images.githubusercontent.com/94822037/160830344-3658d378-2664-4d47-b68c-2f5d9ab28e59.jpg)

다음은 GAB을 통해 Real image를 넣었을 때 나오는 모습 

학습을 시키지 않았 기 때문에 색 정보는 잃고 랜덤한 색으로 형태만 남아있는 모습

학습을 하면 밤 -> 낮 , 낮 -> 밤 에 대한 색인 정보가 GAN 되는 방식이다

### 모델이 동작하는 지만 test 한 단계


위에서 부터 (real 낮 사진 , 낮->밤사진 , real 밤 사진 , 밤 -> 낮사진)

학습 1번 끝났을 때 모습(총 300번 동일 계획)

![epoch 1 result](https://user-images.githubusercontent.com/94822037/160832048-e9895925-b12c-4d3e-b50b-65f9cbf7bfc4.jpg)

학습 5번 끝났을 때 모습(총 300번 동일 계획)

![epoch 4 result](https://user-images.githubusercontent.com/94822037/160832055-5f6401fc-1fe8-4530-9baa-b7bf182fdb2b.jpg)

### result

우선 학습을 많이 돌려 보는게 제일 중요할 꺼 같고 한 학습당 1시간 정도 소요 됨

300시간 소요 








