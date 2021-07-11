# 대회 개요
Image Classification. 이미지를 9개의 재활용 품목 카테고리로 분류하는 문제</br>
Model 경량화. 어느 정도의 성능을 유지하며 크기가 작은 모델을 만드는 문제
![image](https://user-images.githubusercontent.com/75927764/125183136-050d8d00-e24f-11eb-921e-f5e0666257a5.png)

## Score
![image](https://user-images.githubusercontent.com/75927764/125183272-29b63480-e250-11eb-8e35-b70c6cb48b15.png)
- MACs(Multiply-accumulate operations)
  - 합,곱연산의횟수
  - MACs -> 약 0.5FLOPs
</br>

# 대회 결과
![image](https://user-images.githubusercontent.com/75927764/125183337-c7a9ff00-e250-11eb-8e98-a6636d7f159a.png)
Public **2nd**, Private **2nd**
</br>
</br>

# 대회 진행 23일
|Day|LB Score, F1, MACs|실험 내용|실험 내용|실험 내용|
|--|--|--|--|--|
|01||baseline 코드 작성|MACs 공부|EDA|
|02|0.0385 / 0.8205 / 266505|pretrained Model 탐색|MACs 공부|LB score 기준 모델 구하기|
|03|LB 전체 초기화|pretrained Model 탐색|MACs 계산 허점 발견|팀원들과 전략 논의|
|04||pretrained Model 탐색|3가지 분업 전략 설정||
|05|0.4858 / 0.5320 / 1688940|pretrained Model 탐색|Input size 탐색|Inference 분포 확인|
|06|0.4501 / 0.5619 / 1688940|pretrained Model 탐색|LossFunction 탐색|Regularization 기법 적용|
|07|"|pretrained Model 탐색|LossFunction 탐색|Regularization 기법 적용|
|08|0.4192 / 0.5877 / 1688940|Augmentation|Over Sampling|Auto Encoder|
|09|0.4044 / 0.6001 / 1688940|Augmentation|(AutoML) Hyper Parameter 탐색|Auto Encoder|
|10|0.3782 / 0.6220 / 1688940|Knowledge Distillation|(AutoML) Hyper Parameter 탐색||
|11|0.3665 / 0.6318 / 1688940|Knowledge Distillation|ArcFace 실험||
|12|0.3584 / 0.6386 / 1688940|Knowledge Distillation|Residual KD||
|13|0.3513 / 0.6211 / 1299789|Layer Pruning|Feature 분포 확인||
|14|0.3493 / 0.6358 / 1515459|Layer Pruning|||
|15|0.3486 / 0.6318 / 1439700|Layer Pruning|Validation 방법 개선|Train All Data|
|16|0.3476 / 0.6225 / 1272450|Layer Pruning|Auxiliary Training 실험||
|17|0.3362 / 0.6207 / 1083210|Layer Pruning|Channel Pruning||
|18|0.3366 / 0.6158 / 1007460|Channel Pruning|Decomposition||
|19|"|Channel Pruning|Decomposition||
|20|"|Channel Pruning|Decomposition||
|21|0.3362 / 0.6207 / 1083210|실험 결과 종합|최종 모델 선정||
|22|"|회고 및 팀원들과 피드백|발표 준비||
|23|"|발표 준비|||


















# Test Demo
||MACs|F1|Competition Score|
|------|---|---|----|
|Before|1688940.0|0.6206|0.4005|
|After|1083210.0|0.6149|0.3431|

```python
#Before
python test_demo.py --customized_model False --eval True

#After
python test_demo.py --customized_model True --eval True
```
## Envrionment
```
pytorch '1.7.1+cu101'
albumentations
sklearn
ptflops
```
## Directory
```
Model Compression/
├──input/
|  └── data/
|      ├── train/
|          ├── trainImg00001.jpg
|          ├── ...
|          └── trainImg22640.jpg
|      └── val/
|          ├── valImg0001.jpg
|          ├── ...
|          └── valImg8816.jpg
├──pretrained/
│  ├── ShuffleNet_final.pt
│  └── shufflenet_g3_wd4.pth
├──solution/
|  ├── dataloader.py
|  ├── ...
|  └── utils.py
└──test_demo.py
```
