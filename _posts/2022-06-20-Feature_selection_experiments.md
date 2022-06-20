---
layout: post
title: Feature Selection 실험과 고찰
subtitle: 실전으로 적용해보는 Feature selection
categories: ML
tags: [ML, Experiments]
---

**직접 실험하여 공부하고 정리한 기록입니다. 저의 뇌피셜이 많으니 유의해주세요!**   


이론적으로 공부해보고 실제로 Iscream 데이터셋을 활용하여 실험을 진행했습니다.  

(부스트캠프 AI Tech 3기 DKT 대회용 Iscream 데이터셋을 사용했으며 데이터는 제공할 수 없습니다.)  

Pearson’s correlation, LGBM 기반 Feature importance를 사용하여 Feature selection을 시도했습니다.  

<br>

## Base features

userID, testID, assessmentItemID, KnowldgeTage    

(label encoding을 수행한 후 Embedding indexing 접근)   

user_correct_answer : 유저들의 정답 맞춘 갯수를 시간순으로 계산 (NaN,1,2...)   

user_total_answer : 유저들의 문제풀이 수를 시간순으로 숫자를 매김 (0, 1, 2, ...)   

user_acc : user_correct_answer / user_total_answer (실시간 정답률)   

test_mean, test_sum : testId의 총 정답률 계산 및 총 갯수   

tag_mean, tag_sum : KnowledgeTag의 총 정답률 계산 및 총 갯수     

**Target** : answerCode    

**test dataset 기준 AUC : 0.7379**   

<br>

## Feature Engineering

User, Test, Timestamp 측면에서 feature를 결합하거나 재구성하여 23개 feature를 만들었습니다. 무분별하게 feature를 늘리다보니 결이 비슷한 feature가 많다는 생각이 들었습니다. 

Pearson’s correlation을 사용하여 feature간의 correlation을 확인해보고, LGBM 기반 Feature importance를 사용하여 모델에서 어떤 feature가 큰 영향을 미쳤는지 파악했습니다.    

</br>

## Result

### Feature 23개 : test dataset 기준 AUC 0.7885

### Pearson’s correlation

<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/174608471-dfddb30f-d97c-4652-87e3-e6fdd97fc6e1.png"  width="750" height="800"/>
</p>    



### Feature importance (LGBM)

<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/174608860-fcda50d6-9b05-4ab3-b28e-b916ce67421b.png"  width="500" height="500"/>
</p>    


**target인 answerCode를 제외하고 각각의 correlation을 비교했을 때 상관계수가 0.5 이상인 경우**

- ~~user_total_answer~~  : user_correct_answer
- time : mean_time, ~~normalized_time, relative_time~~
- mean_time : ~~relative_time~~
- hour_mode : ~~is_night~~
- ~~relative_time : normalized_time~~
- assess_mean : assess_sum, test_mean, tag_mean
- assess_sum : test_mean, test_sum, tag_mean
- test_mean : tag_mean
- tag_mean : tag_sum       


**answerCode의 overfitting의 원인이 되므로 target을 제외한 두 feature의 상관관계가 큰 경우 feature importance가 상대적으로 낮은 feature를 제거해주기로 결정했습니다.**

**→ 그러나 correlation이 크더라도 feature importance가 크면 일단 제거하지 않았습니다.**   

<br>

## Feature selection

### user_correct_answer, normalized_time, relative_time, is_night 제거 (feature 19개) → validation set 기준으로는 AUC 상승 (0.7441)     


### Pearson’s correlation
<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/174609139-072d3d53-57b5-4fdb-9de9-7174d2e4b6a7.png"  width="750" height="800"/>
</p>     


### Feature importance (LGBM)
<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/174612336-22489bc9-f51c-40d2-94c4-867e0620be95.png"  width="500" height="500"/>
</p>    


- time : mean_time
- assess_mean : assess_sum, test_mean, tag_mean
- assess_sum : test_mean, test_sum, tag_mean
- test_mean : tag_mean
- tag_mean : tag_sum   

<br>

## Feature selection 실험 (validation set 기준 AUC)      

- user_correct_answer, normalized_time, is_night ,relative_time 제거 : 0.7441
- user_correct_answer, normalized_time, is_night ,relative_time, feature_correct, user_total_answer, KnowledgeTag 제거 : 0.7441
- user_correct_answer, normalized_time, is_night ,relative_time, feature_correct, user_total_answer, KnowledgeTag, hour 제거 : 0.7441    

- user_correct_answer, normalized_time, is_night ,relative_time, assess_sum 제거 : 0.7438    


- user_correct_answer, normalized_time, is_night ,relative_time, assess_sum, test_mean, tag_mean 제거 : 0.7388
- user_correct_answer, normalized_time, is_night ,relative_time, assess_sum, test_mean, tag_mean, hour 제거 : 0.7406    


- user_correct_answer, normalized_time, is_night ,relative_time, assess_sum, test_mean, tag_mean, hour, mean_time 제거 : 0.7374
- user_correct_answer, normalized_time, is_night ,relative_time, assess_sum, test_mean, tag_mean, hour, mean_time,    
feature_correct, user_total_answer, KnowledgeTag 제거 : 0.7374    


**→ feature_correct, user_total_answer, KnowledgeTag, hour를 제거해도 성능차이가 별로 없다는 것을 확인했습니다. 해당 feature를 제거하여 feature selection을 시도했습니다.**   

<br>


## Feature 16개 : test dataset 기준 AUC 0.7935 (가장 성능 좋음)    


### Pearson’s correlation    


<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/174613242-da3ada12-b1ef-4025-a1b5-29189bc0c528.png"  width="750" height="800"/>
</p>    


### Feature importance    


<p align="center">
<img src="https://user-images.githubusercontent.com/68064510/174613616-195f3008-92f6-47ff-8a5b-a3a9fe6ae3ce.png"  width="500" height="500"/>
</p>     


Feature importance에서 순위가 낮고, 다른 변수와 상관관계가 큰 feature를 우선적으로 제거하니 overfitting이 감소하여 성능이 향상되었습니다.    

<br>    

### validation dataset 실험

- user_correct_answer, normalized_time, is_night ,relative_time,  
feature_correct, user_total_answer, KnowledgeTag,  
hour 제거  
**0.7441(correct_shift_1 포함 X)  
0.7456(correct_shift_1 포함)**    


- user_correct_answer, normalized_time, is_night ,relative_time,    
user_total_answer, KnowledgeTag      
hour 제거      
**0.7439(correct_shift_1 포함)   
→ 이로써 feature_correct(output과 모든 변수에 대한 correlation이 낮음)는 필요없는 feature로 판단**    


- normalized_time, is_night ,relative_time,    
feature_correct, user_total_answer, KnowledgeTag,    
hour 제거    
**0.7453(correct_shift_1 포함)   
→ 이로써 user_correct_answer는 필요없는 feature로 판단**    


- user_correct_answer, normalized_time, is_night ,relative_time,    
feature_correct, user_total_answer, KnowledgeTag,    
hour, mean_time 제거   
**0.7428(correct_shift_1 포함)   
→ Feature selection 하면 성능 감소 : time 관련 데이터는 feature exctraction을 시도하는 것이 좋다고 판단**    


- user_correct_answer, normalized_time, is_night ,relative_time,    
feature_correct, user_total_answer, KnowledgeTag,    
hour, time_median 제거 (correlation은 높으나 feature importance가 낮음)   
**0.7456(correct_shift_1 포함)    
→ Feature selection 하면 성능 향상**    


- user_correct_answer, normalized_time, is_night ,relative_time,    
feature_correct, user_total_answer, KnowledgeTag,    
hour, time_median, test_sum 제거 (correlation은 높으나 feature importance가 낮음)   
**0.7428(correct_shift_1 포함)    
→ Feature selection하면 성능 하락 : Feature importance가 낮다고 섣불리 제거할 수 없음**   

<br>

## feature 15개 (valid dataset → 0.7456, test dataset → 0.7884)    


validation dataset 기준 가장 성능이 높았으나 실제 test dataset에서는 성능이 잘 안나왔습니다.    

두 지표(Pearson’s correlation, Feature importance)를 활용하여 계속 feature selection을 해보니 valid dataset 기준 ROC 마저 하락하기 시작했습니다.    

**Feature selection을 지속하면 더 하락될 것으로 예상하여 실험은 여기서 마무리했습니다.**   

<br>

## 고찰

Pearson’s correlation, LGBM 기반 Feature importance를 이용하여 feature selection 실험을 했습니다. 수많은 방법이 있지만 기본적으로 많이 사용하는 **Pearson’s correlation**과 **Feature importance**를 사용해봤습니다. 제가 사용한 데이터셋 기준으로 validation dataset보다 test dataset의 **AUC**가 더 높았습니다. 이는 저의 추측 상 test dataset의 경우 **최종문제의 정답여부**를 예측하기 때문에 좀 더 수월하다고 보고 있습니다. 

1. 이론적으로 두 지표를 토대로 feature selection을 시도한다고 해도 성능이 무조건 좋아지는 것은 아니였습니다.    
 → 그래도 **약간의 성능 향상**되는 것을 확인할 수 있었습니다. 데이터 도메인에 대한 지식도 반영되어야할 것 같습니다.
2. 결국 확신이 들지 않아 feature를 추가하거나 제거함으로써 Wrapper method와 비슷한 방식을 취하게 되었습니다.
3. ‘만약 target이 아닌 feature끼리 서로 correlation이 높은 경우 이를 **feature extraction** 시도하면 어떨까?’라는 생각이 들었습니다.  

