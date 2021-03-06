### 전체 구성도

![구성도](https://user-images.githubusercontent.com/94822037/169695603-f6e8a0fb-3a3a-43e5-b9d4-258d64cb2836.PNG)

![최종 구성도](https://user-images.githubusercontent.com/94822037/173185656-96e4abab-4a24-4ef2-86b6-1f7c98d8a91d.PNG)

# 빛번짐 코드 사용 법

https://dacon.io/competitions/official/235746/data 에서 데이터를 다운로드

github에 업로드 되어 있는 코드를 window or ubuntu 등의 환경에서

colab 이나 jupyter notebook 같은 ipynb 파일을 사용 할 수 있는 개발 환경 프로그램을 사용

colab에 각 명시 되어 있는 경로가 저장되어 있는 부분을 자신의 컴퓨터 경로에 맞게 설정

이 후 학습 후 결과를 확인하고 싶다면 github에 업로드 되어 있는 efficientnet.pt를 다운로드 후 모델 선언 부에

model.load_state_dict(torch.load(PATH)) PATH에는 efficientnet.pt의 경로를 입력 

이 후 바로 test를 싱핼 하면 결과 확인 

### 계획

![계획](https://user-images.githubusercontent.com/94822037/165354193-7809b526-9f06-4eeb-a4f7-e8ad8bcd3ef8.PNG)

### 구성도

![캡처](https://user-images.githubusercontent.com/94822037/165354278-4dc06958-57ba-4aa8-8965-07da4edf3995.PNG)

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

### 중간 

![결과](https://user-images.githubusercontent.com/94822037/165354581-48587fae-d7a7-4e7b-af51-7dbf0464e7cb.PNG)

### efficientnet 학습 300번 결과

![test_20010](https://user-images.githubusercontent.com/94822037/165507152-24e9913c-c432-4b1d-9a5a-553e6d32ec62.png)


### 빛번짐 자세한 설명 

우리가 생각한 모델은

빛이 아래 MATRIX를 예로 빛(이미지)이 다음과 같은 형태라고 하면 모델에 넣었을 때 빛이 줄여 들도록 나옴
(모델에 넣으면 이미지에 가중치에 곱한 값이 결과 값이 나옴)
즉 우리는 학습을 통해 다음과 같은 가중치를 만들어 내는 것을 목표로 함


 #        이미지     가중치(목표)       결과
          0001000      0000000        0000000
          0011100      0001000        0001000
          0111110  *   0011100  =     0011100
          0011100      0001000        0001000
          0001000      0000000        0000000

이런 가중치를 가지는 모델을 만드는 것을 목표로 함

인공지능은 이러한 빛의 특징을 가지는 부분을 파악하고 해당 부분에 가중치를 곱해서 빛을 축소하도록 만들어냄

하지만 이런 빛에 대한 특징과 가중치를 얻어내는 곳에 과정이 필요함

### 시나리오

(다음은 예시)
 (28 * 28 * 1) - (14 * 14 *8) - (7 * 7 * 16) - (3 * 3 * 32) -  이미지의 특징을 추출하는 부분 
-(7 * 7 * 16) - (14 * 14 * 8) - (28 * 28 * 1) -  알아낸 특징을 이용해 원하는 이미지 값으로 나오게 하는 부분 (DECODER)

![이미지](https://user-images.githubusercontent.com/94822037/159855713-7a366f31-bd8e-481b-bc76-d5196295a788.png)

모델의 모양을 보면 축소시키는 부분과 확장되어 있는 부분이 대칭으로 이루어져 있는 것을 알 수 있음 


크게 보면 이미지를 축소(ENCODER)하고 다시 확장(DECODER)해내는 과정인데 

기본 적으로 CNN은 이미지를 축소해내가면서 특징을 추출해내는데 사용한다

하지만 우리는 축소를 통해 이미지의 특징만 얻어내는 게 아닌

이러한 특징을 가지고 복구해내는 과정을 가져야 한다

위에 모델이 축소 부분과 확장 부분이 대칭으로 이루어져 있는 이유는

각 축소를 시키는 과정에서 사용된 가중치를 역으로 적용 시키서 우리가 원하는 가중치가 적용된 결과(이미지)를 알아 내기 위해서 인데 자세한 설명은 아래서 한다.


### 모델의 과정 :
cnn - relu - cnn - batchnorm - relu - cnn - relu - fc - 다시 확장하면서 이미지 복구

![encoder](https://user-images.githubusercontent.com/94822037/162760597-677a7e18-a340-4ae7-ad53-c43dc60c703f.jpg)

![decoder](https://user-images.githubusercontent.com/94822037/162760607-64de7f34-1de1-4504-97eb-4797071228bc.jpg)


과정을 하나하나 깊게 탐색하기 전에 각 과정에 사용 되는 함수에 대한 이유를 설명한다.

### CNN

cnn : 이미지와 FEATURE MAP(가중치)을 합성공을 통한 결과 값을 얻어 내기 위해 사용
      (빛 사진에 어떻게 적용 되는 지는 후에 설명)
      그리고 cnn을 통해 이미지를 축소 시킨 이미지를 다시 cnn을 적용하게 되면 더 자세한 특징을 얻어 낼 수 있음

      #cnn에서 가장 중요한 포인트는 빛 사진을 넣었을 때 적용 될 마지막 가중치(cnn을          통해 얻어내기 위해서 인데 모델을 학습 시키고 평가 할 때는 cnn과정의 가장 마지막         에 얻어낸 feature map을 이미지의 matrix와 합성곱을 함으로서 우리가 원하는 결과         가 나오도록 함
       #아래의 가중치를 얻기 위해서임) cnn에서 가중치란 이 feature map을 의미함
        왼쪽과 과 같은 빛의 matrix을 우리가 얻어낸 가중치를 곱해서 결과와 같이 줄여들          게 만듬

          이미지     가중치(목표)       결과
          0001000      0000000        0000000
          0011100      0001000        0001000
          0111110  *   0011100  =     0011100
          0011100      0001000        0001000
          0001000      0000000        0000000
          

### RELU

RELU - 학습을 하면 마지막에 나온 가중치(FEATURE MAP)를 갱신 시키는 과정을 거치게 된다.(왜냐면 학습이 끝나고 다음 학습을 하기전에 전의 학습에 사용 된 마지막 가중치를 가지고오기 때문이다.)

![relu 예시 모델](https://user-images.githubusercontent.com/94822037/162743491-e9d0a4a5-3638-4810-bdfd-472f3d4e6fd3.PNG)

EX) 예를 들어 원하는 값이 70 이고 원래 이미지의 값이 100 이라고 칠 때
    공 지능 모델은 100에서 30을 뻬는 가중치를 다음 학습에 인계하도록 함

하지만 갱신 과정의 마지막에 생긴 가중치(FEATURE MAP)으로 갱신을 시키기 때문에
각 CNN LAYER에 마지막 가중치를 적용시키는 과정에서 손실이 일어 날 수 있음

이러한 손실은 인공지능이 단순히 바로 30 을 뺴는 것이 아닌 CNN LAYER가 3개이라고 할 때   첫번째 LAYER는 100이 95이 되도록 하고 두번째 LAYER는 95에서 85이 되고
마지막 LAYE는 85-70이 되는 방식으로 각 layer에 적용 되는 가중치는 점점 커짐

실제로 우리는 cnn이라는 합성곱을 사용하기 때문에 합성곱을 진행 할수록 가중치는 점점 커지게 됨
갱신은 즉 마지막 layer에서 그 전의 layer로 하나씩 돌아가면서 처음 지점으로 돌아가는 과정임



즉 ,첫 layer에서 마지막 layer로 가는 가중치의 값이 점점 시키기 떄문에
   갱신시에는 마지막 layer에서 첫 번쨰 layer로 돌아가는 과정이기 때문에 마지막 가중치를 
   조금씩 줄여가면서 각 layer에 갱신 시킴 이를 역전파 알고리즘이라고 함

![역전파](https://user-images.githubusercontent.com/94822037/162769435-2589c862-31d2-40bd-b8ce-6976abaef21e.jpg)


 이해안되는 사람들을 위해서 예를 들자면;
 첫 번쨰 layer에서 두 번째 layer에 검출된 가중치를 2라고 하고
 두 번째 layer에서 세 번쨰 layer에 검출된 가중치를 3이라고 하고
 세 번쨰 layer에서 검출된 마지막 가중치를 4라고 하자

 그럼 모델은 마지막 가중치가 4가 되도록 이전의 layer의 가중치를 조정해냄 보통 이과정은 마지막 과정을 미분함으로서 가중치를 점점 줄여 나가고 이를 가중치 갱신이라고 한다

하지만 이렇게 미분을 하는 과정에서 더 이상 미분을 할 값이 없다면 더 이상 가중치 갱신을 하지 않게 되고 가중치 내용에 대해서 손실이 일어나게 된다.

이를 기울기 소멸 문제라고 함

![기울기 소멸](https://user-images.githubusercontent.com/94822037/162769437-d0b8e0ce-393f-4fd8-88b3-6f5bc7e786e0.jpg)



이는 FEATURE 맵을 적용하고 값이 음수가 나오게 되는 경우에 발생
그래서 RELU는 0과 음수부분에 해당 되는 부분을 전부 0으로 만들어서
가중치를 갱신하는 과정에서 손실이 일어나는 걸 방지하는 과정임

즉 relu는 갱신 중 음수가 나와서 기울기 소멸이 알어나는 걸 방지 하기 위해서 사용함

그래프로 표현해 보면 다음과 같음

![relu](https://user-images.githubusercontent.com/94822037/162743448-3ef6f519-91fb-4ebc-8ccc-57d43ea331cf.PNG)


### BATCHNORM

BATCHNORM2D - CNN을 적용시 각 FEATURE 맵은 다른 가중치를 가지고 있을 것이고
                      실제로 FEATURE을 적용하고 데이터를 분포화 시켜보면 각각 다른 결과를 내게 됨 BATCHNORM은 FEATURE MAP에 통해 나온 결과의 평균과 표준편차로 계산해서 각 FEATURE맵에 대한 가중치를 평균 , 표준편차를 사용해 정규화 시킴

쉽게 데이터가 선형으로 가정하고
빨간 선이라는 데이터와 파랑선이라는 데이터에 대한 결과 값이 있고 이에 대한 batchnorm을 사용하게 되면 점선의 형태가 되는 것임

![정규화](https://user-images.githubusercontent.com/94822037/162770210-aa27c3b8-388a-4c51-9830-ba00d698399f.jpg)


### 요약

최종 :

첫 번 째 : cnn을 통해 이미지의 특징을 추출함과 동시에 이미지를 축소시킴(세부적 인 특징을 추출함)

두 번 째 cnn을 통해 나온 결과가 음수일 시 가중치를 갱신 해내는 과정에서 손실이 일어 날 수 있기 때문에 relu을 통해 이를 방지

세 번 째 : 다시 cnn을 이용해 축소 된 이미지에 대한 더 세부 적인 특징을 얻어냄

다시 relu을 적용해서 기울기 소멸 방지

네 번 째 : 지금 까지 나온 특징의 feature map등을 정규화를 시켜 합성곱 결과가 일치하도록 만들어줌

1~4번 째 과정 후 

fc는 fully connect layer의 약자로 마지막에 적용된 이미지의 모양을 일차원으로 변형 시키고 마지막 가중치를 적용시켜줌 우리는 이미지의 특징을 추출 할 뿐만 하니라 다시 원래의 이미지의 형태로 되돌려야 하기 때문에 1차원으로 변형시킨후 이를 최종 가중치를 적용 시켜 특징이 적용된 결과 값을 다시 원래 이미지로 복구하는 과정을 거치게 됨

나머지는 이 이미지를 다시 대칭에 맞게 복구 하는 과정

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


### discrimior

위조 지폐를 예를 들어서 설명을 해보면 위조범이 위조 지폐를 만드는데 이 위조 지폐가 진짜 지폐를 구분할 수 없게 만드는 것이 목적 

discrimior는 위 설명에서 위조범을 이야기한다

즉 우리가 만일 밤사진을 가지고 위조 낮사진을 만들어 내면 이 discrimior가 위조 낮사진을 진짜 낮사진이라고 착각하게 만드는 것이 목적

### 모델이 동작하는 지만 test 한 단계


위에서 부터 (real 낮 사진 , 낮->밤사진 , real 밤 사진 , 밤 -> 낮사진)

학습 1번 끝났을 때 모습(총 300번 동일 계획)

![epoch 1 result](https://user-images.githubusercontent.com/94822037/160833047-8e91dadb-59a0-47fb-90a4-99c372f0b82c.jpg)

학습 5번 끝났을 때 모습(총 300번 동일 계획)

![epoch 4 result](https://user-images.githubusercontent.com/94822037/160832055-5f6401fc-1fe8-4530-9baa-b7bf182fdb2b.jpg)

### result

우선 학습을 많이 돌려 보는게 제일 중요할 꺼 같고 한 학습당 1시간 정도 소요 됨

300시간 소요 예상 

### cycle gan 참고 코드(horse to zebra)

https://www.kaggle.com/code/balraj98/cyclegan-translating-horses-zebras-pytorch


### 현재 밤낮 전환 상태

예상은 했지만 너무 어려운 부분 같은 경우 아예 바꾸지 못하거나 이상한 사진으로 대체해버리는 경우가 발생중

### 좋은 데이터로 평가

![예시](https://user-images.githubusercontent.com/94822037/168463825-98dcd298-a0db-40e1-92d6-fd277fda5204.png)

### 너무 어두워서 안좋은 데이터 평가

![안좋은 예시](https://user-images.githubusercontent.com/94822037/168463829-9b92de1a-ac55-40eb-ba84-591c0d5c9cf2.jpg)

### 협업 진행 상황

모댈의 구조를 바꾸면서 진행 중

밤낮 전환 인공지능 또한 이미지를 축하고 확장하는 과정을 거치는데 

1~4까지 각 축소의 횟수에 따라 달라지는 결과를 비교


### 축소에 따른 결과

![축소 확장](https://user-images.githubusercontent.com/94822037/173001014-31e2316d-e840-449c-bf7c-c0da12537f6e.jpg)

시각적으로 봤을 때 2번 축소 후 확장이 가장 잘 바꾸는 것을 확인

이후 모델 구조중 축소 -> 특징 전이 -> 확장 에서 특징 전이의 역할을 하는 resdual block의 층 수를 6,9,12,15로 맡아서 결과를 확인 할 

### 특징 전이 횟우에 따른 결과

![특징 전이](https://user-images.githubusercontent.com/94822037/172087073-0a69057f-e4d8-4f55-a0ec-5c0e3b75167c.PNG)

9번 축소 및 확장 했을 때 결과를 채텍

### 빛번짐 , 밤낮 전환 인공지능을 모두 사용 했을 때

![최종 결과](https://user-images.githubusercontent.com/94822037/172087095-7c6b40f8-8990-42c1-95f9-b2e40af59bca.PNG)
