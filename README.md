### model
![이미지](https://user-images.githubusercontent.com/94822037/159855713-7a366f31-bd8e-481b-bc76-d5196295a788.png)

autoencoder 모델은 입력 데이터(input data)를 압축시켜 압축시킨 데이터로 축소한 후 다시 확장하여 결과 데이터를 입력 데이터와 동일하도록 한드는 일종의 딥 뉴럴 네트워크

이 모델은 자기 자신을 결과로 나오게 만들지만 우리는 모델 마지막에 sigmoid를 추가 즉 autoencoding을 통해 이미지의 특징을 추출하고 output이 나오도록 한다.

###   unet++

![unet++](https://user-images.githubusercontent.com/94822037/159856694-1c1e489c-9841-4ca3-abd0-4687f8dca587.PNG)


최종 사용하는 모델의 모형은 다음과 같다.
backbone부분에는 전이 학습 모델을 가져와서 이미지를 축소 시킨다(x0.0~x4.0) 이 후 다시 이미지를 확장 시킨다(x4.0~x0.4)

unet에서 res path부분에도 convolution 연산을 수행 시키는 모델이 unet++이다 결과적이로 x0.1~x0.4의 평균 값 혹은 x0.4의 값을 결과 값으로 사용한다.

### result

![첫 결과](https://user-images.githubusercontent.com/94822037/159857508-95429dee-2ff8-441a-aa96-f5a8ee1f802a.PNG)

왼쪽 부터 input image , label image , output image이다.

현재는 DACON에서 개최하는 LG의 data를 가지고 빛번짐 성능을 test 해보았다.

이제 실제 목적인 블랙 박스상 가로등 , 달빛등의 빛번짐을 제거하려고 한다.




