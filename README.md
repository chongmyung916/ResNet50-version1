# ResNet50
ResNet50 model classification(melanoma)
ResNet50 모델을 사용해서 흑색종 피부암(melanoma)를 예측했다.
- 데이터는 2019년 데이터와 2020년 데이터를 train:val:test 7:2:1 비율로 split했다.
- 2020년도 데이터(약 3만 3천장), 2019년도 데이터(약 4천장, melanoma를 잘 예측하기 위한 melanoma sample data)

# ResNet50-version1
<실험 세팅>
- 훈련 데이터에 대해서 증강 적용(val, test data에서는 증강 적용 x)
- 하이퍼파라미터 설정
  batch_size = 32
  lr = 0.001
  l2_reg = 0.0001
  num_epochs = 20
- WeightedRandomSampler 적용
<성능 분석 결과>
- ROC Curve 분석 결과: auc 값이 0.984로 높음(모델이 전반적으로 좋은 분류 성능을 보인다는 것을 의미)
- Confusion matrix 분석 결과:
  TP(melanoma인것을 melanoma로 잘 예측한 경우): 494
  FN(melanoma인것을 benign으로 잘못 예측한 경우): 17
  FP(benign인것을 melanoma로 잘못 예측한 경우): 693
  TN(benign인것을 benign으로 잘 예측한 경우): 2562
  Melanoma(흑색종)에 대한 False Negative(17건)가 적어, 모델이 Melanoma를 잘 감지하고 있다는 점은 긍정적이다.
  그러나 Benign(양성) 데이터를 Melanoma로 오분류한 경우(693건)가 많습니다. 이는 정밀도(Precision) 저하로 이어진다.
- test Result
Accuracy: 81.15% (Class 0: 78.71%, Class 1: 96.67%)
AUC: 0.9840
Precision: 0.4162
Recall: 0.9667
F1 Score: 0.5819
- test 성능 분석 결과
  - Melanoma(흑색종) 탐지는 매우 잘하지만, 정밀도가 낮다.
    FP가 많아 Melanoma로 잘못 예측하는 경우가 많다.
  - Class Imbalance 영향
    Benign(양성)이 많고 Melanoma(악성)이 적은 데이터 분포로 인해 모델이 Melanoma를 과하게 예측하는 경향
    Precision이 낮고 Recall이 높은 경우, 모델이 Melanoma를 놓치지 않기 위해 더 많은 샘플을 Melanoma로 분류했을 가능성이 큼
<대안>
- False Positive 감소 전략
  Hard Negative Mining: 잘못 분류된 Benign 데이터를 분석하고 추가 학습
  모델에 더 많은 Benign 이미지를 학습시키거나 데이터 증강(Augmentation) 활용
- threshold 조정
  현재 모델은 False Negative를 최소화하는 방향으로 학습된 것으로 보임
  False Positive를 줄이기 위해 기준 임계값(Threshold)을 높이는 방법 고려
  ROC 커브에서 특정 지점을 선택해 Precision과 Recall 균형을 맞추는 방법
<결론-version1>
모델은 Melanoma를 잘 탐지(Recall ↑)하지만, Melanoma로 잘못 예측(Precision ↓)하는 경우가 많다.
피부암 진단에서는 False Negative가 중요한 문제이므로 재현율이 높은 것은 장점이지만, Precision도 고려해야 한다.(threshold 조정, class imbalance 해결, FP 감소)
낮은 Precision을 높이는 것이 중요하다고 판단했다.
