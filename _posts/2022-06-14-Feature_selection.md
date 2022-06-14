---
layout: post
title: Feature selection
subtitle: 정보의 바다, 어떤 정보가 중요한가?
categories: ML
tags: [ML]
---

**Reference를 참고하여 공부하고 정리한 기록입니다. 저의 뇌피셜도 존재할 수 있으니 유의해주세요!**

<br>

많은 정보 속에서 우리에게 필요한 정보만 찾아내는 것은 매우 중요합니다!

모델도 마찬가지 입니다! 

머신러닝 모델에서 간단하지만 중요한 rule이 존재합니다.

<br>

> **goes in, comes out**
> 

<br>

즉, 쓰레기를 넣으면 쓰레기가 나온다(**garbage in, garbage out**)고 받아들일 수 있습니다.

하지만 데이터에는 noise가 존재할 수 밖에 없습니다. 

우리의 **목적(target)에 부합하는 feature**만 선택하는 작업이 필요합니다.

<br>

## Feature extraction의 장점은 무엇인가?

- 차원의 저주 방지
- 불필요한 데이터를 줄임 → 훈련시간 축소
- generalization 효과 → overfitting 해소

<br>

## Feature selection은 어떻게 할까?

feature selection에서도 다양한 방법이 존재합니다. 크게 supervised, unsupervised 로 구분됩니다.

<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/173487682-51965c4b-5e3a-4ab4-9b64-f79a51a71972.png"  width="400" height="250"/>
<p>

1. Supervised models : output label에 가장 부합하는 feature를 선택합니다. target에 부합하는 feature를 효율적으로 선택할 수 있습니다.
2. Unsupervised models : labeling이 없는 데이터를 활용하여 feature selection을 합니다.
    
    *→ target 없이 어떻게 feature를 선택할 수 있을까? 이에 관해서는 추후 공부를 더 해볼 예정!*
    
<br>

**supervised model에서는 Intrinsic(Embedded method로 칭하기도 함), Wrapper method, Filter method이 있습니다.** 

<br>

### Filter Method

통계적 측정방법을 사용하여 **feature의 상관관계**를 찾는 방법

<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/173487704-0e9e3078-5271-49f1-9ba3-7c4dd249bef0.png"  width="200" height="250"/>
<p>

- 반복 과정이 없어서 계산 속도가 빠릅니다.
- feature간의 상관계수를 통해 feature subset을 구하는 것이 최적의 방법이 아닐 수 있습니다.
- Example
    - information gain
    - chi-square test
    - fisher score
    - correlation coefficient
    - variance threshold

<br>

### Embedded Method (Intrinsic)

내장 metric을 사용하여 **유용성을 측정**하는 방법

<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/173487727-5be37f8b-a540-40f8-bc67-34c8e82e346f.png"  width="200" height="250"/>
<p>

- **좀 더 적은 계수를 가지는 회귀식**을 찾는 방향으로 제약조건을 주어 이를 제어합니다.
- Example
    - LASSO (L1 norm)
    - Ridge (L2 Norm)
    - Elastic Net (L1 norm + L2 norm)
    - Select From Model (Decision tree 기반 알고리즘에서 feature importance를 사용)

<br>

### Wrapper Method

**유용성을 측정**하는 방법

<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/173487751-ca806e8d-e99f-4ac0-a8a4-7f9d1f41bce6.png"  width="200" height="250"/>
<p>

- 예측 모델을 사용하여 feature subset을 계속 테스트하는 **greedy한 접근방식** → 연산량이 많다.
- Example
    - recursive feature elimination (RFE)
    - sequential feature selection (SFS)
    - genetic algorithm
    - univariate selection
    - exhaustive
    - minimum redundancy maximum relevance (mRMR)
    
<br>

이를 크게 분류하자면 3가지의 방식이 있습니다.

- **Forward selection** : 변수 없는 상태에서 시작 → 성능 향상이 없을 때 까지 변수를 추가
- **Backward elimination** : 모든 변수를 가진 상태에서 시작 → 성능 향상이 없을 때 까지 변수를 제거
- **Stepwise selection** : Foward selection + Backward elimination

<br>

## 어떤 방법을 선택해야할까?

매우 많은 방식을 다 실험해보는 것은 비효율적입니다. 위의 3가지 supervised model을 참고해서 적절한 방법을 선택할 수 있지만 **input과 output의 variable의 타입**에 따라 선택할 수 있습니다.

| Input Variable | Output Variable | Feature Selection Model |
| --- | --- | --- |
| Numerical | Numerical | • Pearson’s correlation coefficient <br> • Spearman’s rank coefficient |
| Numerical | Categorical | • ANOVA correlation coefficient (linear) <br> • Kendall’s rank coefficient (nonlinear). |
| Categorical | Numerical | • Kendall’s rank coefficient (linear) <br> • ANOVA correlation coefficient (nonlinear). |
| Categorical | Categorical | • Chi-Squared test (contingency tables) <br> • Mutual Information. |

- **Numerical Variables** : integers, float, and numbers.
- **Categorical Variables** : labels, strings, boolean variables, etc.

<br>

## 회고하자면...

다양한 feature selection 방법을 공부하고 이를 선택하는 기준을 알아봤습니다. 차원 축소를 하더라도 정확한 결과를 얻기 위해 많은 연구가 이뤄졌음을 확인할 수 있었습니다. 효율을 찾기 위해 시도한 방법들이 오히려 비용 소모의 원인이 되지 않을 지는 고민해봐야할 문제입니다.

1. 추후 **실험**을 통해 feature selection이 잘 되는지를 확인해볼 것입니다. 
2. **통계적 지식**이 부족하여 이를 좀 더 보강할 예정입니다. 
3. 초반에 **unsupervised 방법**으로 feature selection이 가능하다고 했는데 feature extraction과 같은 개념으로 바라본 것인지 파악하기 힘들었습니다. target에 영향을 주는 feature를 선택한다는 점에서 supervised model이 대다수라는 생각을 해봤으나, target이 없을 때 어떤 기준으로 feature를 선택할 지에 대한 감이 오지 않아서 자료조사를 해볼 것입니다.

<br>

## Reference

1. [“Beginner Guide : Feature Selection”](https://subinium.github.io/feature-selection/), Hello Subinium!, 2019.09.04 수정, 2022.05.13 접속
2. [“Introduction to Feature Selection methods with an example (or how to select the right variables?)”](https://www.analyticsvidhya.com/blog/2016/12/introduction-to-feature-selection-methods-with-an-example-or-how-to-select-the-right-variables/), Sauravkaushik8 Kaushik, 2016.12.01 수정, 2022.05.13 접속 
3. [“Everything You Need to Know About Feature Selection In Machine Learning”](https://www.simplilearn.com/tutorials/machine-learning-tutorial/feature-selection-in-machine-learning), Kartik Menon, 2021.09.17 수정, 2022.05.13 접속