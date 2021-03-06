# Korean-ABSA-Dataset
**2021 KSC 한국어 속성기반 감성 분석을 위한 음식 리뷰 도메인 데이터셋 제작 및 평가**

## Review Dataset
- 음식 리뷰 도메인으로 네이버 지도 내 음식점, 요기요 리뷰 사용
- 속성 용어 5개: `가격`, `맛`, `배달`, `서비스`, `양`
  - 속성 별 감성: `긍정`, `중립`, `부정`
- 데이터 형식 및 예시:
  - 문장, 속성 용어, 속성 감성 순서로 기재
  - 문장 내 속성 용어는 `$T$`로 대체 처리 
  - `긍정`: 1, `중립`: 0, `부정`: -1
```
$T$이 넘 늦어요 2시간 걸렸어요ㅡㅡ
배달
-1
```
- 속성 용어와 감성별 데이터 분포
<img src="https://user-images.githubusercontent.com/38764035/146960812-37bd6b45-3d6b-44aa-8648-fab51a1511d3.png" alt="drawing" width="500"/>

## Baseline 성능 평가
- 구축한 데이터셋의 baseline 성능 평가를 위해 기존 ABSA 모델 활용
- Codes mostly from [ABSA-Pytorch](https://github.com/songyouwei/ABSA-PyTorch)

### 한국어 처리를 위한 변경사항
#### KoBERT clone 
```python
pip install git+https://git@github.com/SKTBrain/KoBERT.git@master
```
or
```python
git clone https://github.com/SKTBrain/KoBERT.git
mv KoBERT kobert
cd kobert
pip install -r requirements.txt
```
#### FastText 한국어 embedding 다운로드
- BERT 미사용 모델들을 위해 필요
- [FastText](https://fasttext.cc/docs/en/crawl-vectors.html) 에서 Korean ```cc.ko.300.bin``` 다운로드

### 2가지 한국어 Pre-trained BERT 사용
- 한국어 처리를 위해 multilingual BERT & KoBERT 두 가지 나누어 사용
- multilingual BERT: `_mult`
- KoBERT: `_kr`
- non-BERT 모델들의 경우 혼용하여 사용 가능
``` python
# pre-trained Multilingual BERT (base, uncased)
data_utils_mult.py
train_mult.py

# pre-trained KoBERT
data_utils_kr.py
train_kr.py

# 공통적으로 사용하는 파일 (for inference)
infer_example_kr.py
```

### How to train
For example:
``` python
# pre-trained KoBERT + BERT SPC
python train_kr.py --model_name bert_spc --lr 1e-5

# pre-trained multilingual BERT + LCF BERT
python train_mult.py --model_name lcf_bert --lr 2e-5 --l2reg 1e-5 --embed_dim 768 --hidden_dim 768 --dropout 0

# AOA (train_mult.py 사용 무관)
python train_kr.py --model_name aoa --lr 1e-3 --num_epoch 30 --l2reg 10e-4 --dropout 0.2

```

### 성능 평가 결과
- 정확도(Accuracy), Macro-F1 score 측정
- 각 모델별 10회씩 실험 후 평균, 표준편차 측정

#### BERT 미사용 모델
<img src="https://user-images.githubusercontent.com/38764035/146962735-9df5de4d-8a94-4772-8daa-db683160aa52.png" alt="drawing" width="500"/>

#### BERT 사용 모델
<img src="https://user-images.githubusercontent.com/38764035/146962774-43e67712-8a4b-4a6a-b78a-ede7aa1649ea.png" alt="drawing" width="500"/>



