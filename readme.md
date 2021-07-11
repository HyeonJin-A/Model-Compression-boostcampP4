# Index
1. [대회 개요](#1-%EB%8C%80%ED%9A%8C-%EA%B0%9C%EC%9A%94)
2. [대회 결과](#2-%EB%8C%80%ED%9A%8C-%EA%B2%B0%EA%B3%BC)
3. [대회 진행 23일](#3-%EB%8C%80%ED%9A%8C-%EC%A7%84%ED%96%89-23%EC%9D%BC)
4. [실험 내용](#4-%EC%8B%A4%ED%97%98-%EB%82%B4%EC%9A%A9)
5. [Test Demo](#5-test-demo)

---
</br>

# 1. 대회 개요
Image Classification. 이미지를 9개의 재활용 품목 카테고리로 분류하는 문제</br>
Model 경량화. 어느 정도의 성능을 유지하며 크기가 작은 모델을 만드는 문제
![image](https://user-images.githubusercontent.com/75927764/125185440-ce8c3e00-e25f-11eb-9b30-8337fc33f6da.png)

## Score
![image](https://user-images.githubusercontent.com/75927764/125185456-dba92d00-e25f-11eb-91aa-fa6a35774179.png)
- MACs(Multiply-accumulate operations)
  - 합,곱연산의횟수
  - MACs -> 약 0.5FLOPs
</br>


# 2. 대회 결과
![image](https://user-images.githubusercontent.com/75927764/125183337-c7a9ff00-e250-11eb-8e98-a6636d7f159a.png)
Public **2nd**, Private **2nd**
</br>
</br>

# 3. 대회 진행 23일
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

</br>


# 4. 실험 내용
## Pretrained Model Search
![image](https://user-images.githubusercontent.com/75927764/125185208-6b4ddc00-e25e-11eb-97b1-c74718f0d6dd.png)
SOTA, 논문, 라이브러리 등을 참고하여 가벼워 보이는 모델들을 여러가지 구글링 해봤는데, pretrained weight가 존재하는 모델 중에서는 ShuffleNet이 MAC대비 F1 스코어가 가장 좋았습니다.
</br>선택한 ShuffleNet은 스템을 제외하면 stage 3개로, 각 4-8-4개의 유닛 구조로 되어있습니다.
</br>

## Feature Distribution
Stage1</br>
![image](https://user-images.githubusercontent.com/75927764/125185343-59206d80-e25f-11eb-998e-4a3bdfd1bac4.png)
</br>Stage2</br>
![image](https://user-images.githubusercontent.com/75927764/125185358-66d5f300-e25f-11eb-887c-63238aee617b.png)
</br>Stage3</br>
![image](https://user-images.githubusercontent.com/75927764/125185372-71908800-e25f-11eb-961e-517083bf967f.png)
</br>ShuffleNet에 경량화 기법을 적용하기 위해 Weight 분포를 확인해본 결과입니다.</br>
std(표준편차)값이 클수록 0이 아닌 weight 값이 많이 분포되어 있으므로, std가 낮은 레이어부터 제거하였습니다.
</br>

## Model Compression
![image](https://user-images.githubusercontent.com/75927764/125185421-b1f00600-e25f-11eb-8967-506b272fae1f.png)
- MACs를 직접적으로 감소시키기 위해 Input size를 80x80으로 매우 낮게 설정 하였습니다.
- Input size 축소로 인해 추출할 feature 수가 줄어 들었다고 판단하여 Network 사이즈를 축소 하였습니다.
- 대회 특성을 고려하면 unstructured pruning은 불가능하고, structured pruning (layer, channel) 및 weight decomposition를 시도 하였습니다.
- layer pruning : [4-8-4] -> [2-5-2]
- channel pruning : stage3 [120, 240] -> [120, 210]
- decomposition : stage3 conv group3 -> group6
</br>

## Knowledge Distillation 실험
![image](https://user-images.githubusercontent.com/75927764/125185602-bff25680-e260-11eb-8cad-ea4851f61d8f.png)
</br>실험 결과 다음과 같은 결론을 내렸습니다.
- 비슷한 분포를 가지는 모델을 teacher로 사용하는 것이 매우 효과적
- 분포가 조금 달라져도, teacher를 Ensemble하는 방법은 효과적

</br>
</br>

# 5. Test Demo
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
