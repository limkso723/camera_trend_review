# 카메라 트랜드 및 리뷰 분석 ≪ 총 2주, 팀프로젝트 ≫

## 진행기간 
- 2022.07.11 ~ 2022.08.04

## 개요
- **카메라 트랜드 및 리뷰 분석을 통한 카메라 방향성 제시 가능**
  - 키워드 탐색 
    - 문장에서 가장 많이 거론되는 키워드 탐색 
  - 키워드 시각화
    - 히스토그램과 N_gram을 이용한 키워드 시각화

## 위치eㅔ너지 팀   

- 
-
-
-
-
___
## 파일 설명

- 1_
  - 
  

- 2_
  - 

## 사용된 데이터  

- [기사 크롤링](http://kr.aving.net/)
- [네이버 쇼핑 크롤링 데이터](https://shopping.naver.com/home)

## 과정  

 1. 개발환경 : Colab
 
 2. 데이터 크롤링
 
 3. 데이터 전처리
   - 전처리
     - Okt 활용
       노이즈 & 불용어 제거
       명사, 동사, 형용사 추출

 4. 데이터셋 조합
   
 데이터셋 | 데이터 갯수 | 
 :-------:|:-----------:|
 train + dev | 6334|         
 data + similar | 6735|       
 data + 108 | 6442 |         
 data + manual | 6623 |        
 data + all | 7826|          
 data + 10_17 | 7175|       
 data +108_1017_10_24| 7365|       
 final_data | 9641     |  

 4. 데이터셋 분석

  ![CNN-Figure-01](https://user-images.githubusercontent.com/114709607/201512983-cbcd6399-d378-4936-a48d-74a5eea11889.png)  
  
  - 위의 표는 데이터별 가장 좋은 f1 점수를 나타낸 것이다. 표를 보면 알 수 있듯이 증강된 데이터보다 외부데이터를 추가했을 때 f1 점수가 높고 데이터별로 라벨 수, 데이터 수, component의 수에 따라 영향이 있다는 것을 알 수 있음. 

    - 데이터별로 적합한 라벨이 있다.
      : data를 보면 학습데이터에서 2개의 레이블이 없음에도 불구하고 23labels보다는 25labels의 f1 점수가 더 좋은 것을 알 수 있다. 반면, data+108+10_17+10_24는 학습데이터에         2개의 레이블을 채웠음에도 불구하고 25labels보다는 23labels이 좀 더 유의미했다.

    - 레이블 간의 불균형을 해소해줘야 한다. 
      : data보다 data+α로 증강을 하거나 외부데이터를 추가하여 불균형을 해소했을 때 좀 더       유의미한 결과를 얻을 수 있다.

    - component는 여러 개를 뽑아내는 것이 조금 더 좋으나 비슷하다.
      : data+10_17과 data+108+10_17+10_24를 비교해보면 component가 n개인 data+10_17이 좀 더 좋지만 0.01차라서 component에 따른 영향은 없다고 볼 수 있다. 
    - 따라서, 레이블 간 불균형이 심할 때는 증강하는 방법보다는 외부데이터를 추가하는 것이 좀 더 효과적이며 외부데이터는 찾고자 하는 데이터의 성질과 비슷하여야 좋은 결 과를       얻을 수 있다는 것을 알 수 있다. 

 
 5. 모델 구축
   - BaseLine
     - BaseLine 모델을 기반으로 ELECTRA, BERT, RobertA를 검증하였다.  
       결과적으로 ELECTRA 모델의 성능이 제일 좋게 나왔으며, 한글 리뷰데이터를 학습시킨,
       [kykim/electra-kor-base](https://github.com/kiyoungkim1/LMkor)를 사용하였다.
       ![CNN-Figure-03]( https://user-images.githubusercontent.com/114709607/201506095-9f7056b0-fc6f-4a89-9e2a-f8a2a808a7e2.png)  
     - 속성범주 탐지와 속성감성 탐지 두개의 모델로 나눠져 있으며, 같은 문장이라면 속성범주가 달라도 속성감성이 같은 점을 이용해,
     각 모델의 최고값을 찾아 서로 concat 하는 방법을 사용하였다.
 
 6. 앙상블 기법
   - 소프트 보팅
     -  pt값을 이용해 소프트 보팅을 시도하였다. 
   - 하드보팅
     - 여러 모델의 결과값을 2개, 3개, 5개, 12개 조합으로 하드보팅을 시도하였다. 주로 3개 조합에서 좋은 결과를 얻었다.  


## 결과
- pre-trained 된 모델일지라도 어떤 데이터로 학습을 했는지에 따라 결과가 다르다는 것을 알 수 있다. 본 경진대회는 리뷰데이터로 속성기반 감성분석을 하는 것이 과제였으므로 다양한 리뷰데이터가 학습된 모델들을 앙상블 함으로써 좋은 결과를 얻을 수 있었다. 또한, 문장 당 개체#속성과 감성을 따로 학습하여 결과를 나타내기에 2개의 모델이 필요하고 앙상블 할 때는 entity_property는 polarity를 고정해놓고 앙상블을 하고 polarity는 entity_property를 정해놓고 앙상블을 하여 마지막에 각각 개별로 앙상블 된 것을 합치면서 성능을 올릴 수 있었다.
  
  

## 보완점
- 데이터 핸들링을 다양하게 했음에도 불구하고 단일 모델에서 61% 이상 오를 수 없었다. 단일 모델의 성능을 끌어올리기 위해 외부데이터 추가 시 모델의 학습에 적합하게 만들어주기 위한 다양한 방법을 모색함과 동시에 앙상블 최적의 조합을 찾기 위해서 앙상블을 하지 않았던 데이터들을 검토해봐야 할 것 같습니다.

## 참조
- Clark, Kevin, et al. "Electra: Pre-training text encoders as discriminators rather than generators." ICLR 2020.
https://arxiv.org/abs/2003.10555
