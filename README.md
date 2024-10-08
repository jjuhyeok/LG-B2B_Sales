# LG-B2B_Sales


주최 : ```LG AI Research + LG 전자```

규모 : 총 2000여명 참가
  

  
## 🧐 About
MQL((Marketing Qualified Leads) 데이터를 활용하여 ```영업 기회 전환을 예측하는 AI 모델을 개발```하는 대회

✏️
**대회 느낀점**  

이번 Task의 평가 지표는 F1 Score였습니다. 데이터의 EDA 및 모델링 결과를 통해 특정 피처(customer_idx, lead_owner)에 대해 엄청난 과적합을 보임을 확인하였습니다.(Train에 존재하지 않는 unique 값들로 Validation Score를 비교하였을 때, 엄청나게 F1 Score가 떨어짐을 확인 + Feature_importance가 저 두개의 피처가 엄청나게 높음)  
따라서 모델 정확도를 평가 받는 이번 대회의 특성상 Test 데이터에 Train에 존재하는 customer_idx, lead_owner피처의 unique값이 들어있다면 그 고객(샘플)의 예측에 대해서는 높은 정확도를, 반대로 Train에 존재하지 않는 customer_idx, lead_owner피처의 unique값이 들어온다면 그 고객(샘플)의 예측에 대해서는 낮은 정확도를 기록할 것입니다.  
그렇기 때문에 사실상 제공된 Test 데이터의 통계를 뜯어본다면 위와 같은 정보를 이용하여 F1 Score의 점수를 비정상적으로 올릴 수 있습니다.  

하지만 이는 치팅이라 생각하였고, 실제 세계에서 Test 데이터는 볼 수 없기 때문에 최대한 강건한 모델을 만들어 발표 평가에 들어 이 부분을 핵심적으로 어필하자는 계획을 세웠습니다.   

따라서 다음에 설명 드릴 전력으로 모델링을 진행하였습니다.  
하지만, 결론적으로 F1 Score가 극단적으로 높지 않아 발표 기회를 놓쳤습니다.  

그렇게 최종 1등의 발표를 들었습니다.  
최종 1등 팀은 위에서 언급하였던 customer_idx의 피처를 엔지니어링 하여 핵심 피처로 선정하였고 그 후 자신들의 전략은 강건한 모델링이라고 하였습니다.  

저의 입장에서는 사실상 이해가 되지 않는 발표였습니다.  

customer_idx는 대회에 참여했던 대부분이 동의한 Overfitting을 유발하는 피처이고, 이러한 피처로 Engineering을 하여 Customer_idx관련 피처들을 만드는건 더 Overfitting을 유발하는 것이 아닌가? Test 데이터셋이 Train 에 존재하지 않는 unique값 들로 이루어졌으면 저 모델은 예측이 정말 되지 않을 텐데 라는 의문이 들었습니다.

따라서 발표를 들은 후 최종 1등팀에게 이 문제에 대해 질의를 하였지만, 몇번의 대화 끝에 그 팀에게서 L1 규제 하이퍼파라미터가 알아서 해줄 것이라 생각했다는 답변을 들었습니다.  

사실상 이 팀은 제가 제일 중요하게 생각했던 부분을 생각하고 모델링을 진행하지 않았구나라고 느껴졌습니다.  

나도 Test에 오버피팅시키도록 했어야 했나 라는 약간의 아쉬움이 들었습니다.

이 글을 쓰는 순간에도 더 강건한 모델은 우리 모델이 아닌가? 라는 의문이 풀리지 않습니다.

물론, 배울점도 있었습니다. 최종 3등을 차지한 팀은 자연어를 이용하여 분류 작업을 수행하였습니다. 피처들과 value를 이용하여 문장을 만들어 이 문장들을 합쳐 문단을 만들어 그대로 Bert 모델의 input으로 이용하였습니다. 이 아이디어는 신선하였고 이러한 데아터셋에서는 머신러닝 모델만 사용해야 한다는 저의 고정관념을 깰 수 있었던 좋은 경험이었습니다.




<br>
<br>


## 🔥**대회 접근법 및 배운점**
이번 대회는 고객의 영업 전환 여부에 대한 Task였습니다.  
데이터는 고객 정보, 영업 및 마케팅 정보, 사업 단위 및 영역, 성능 지표 그리고 타겟값인 영업 전환 여부에 대한 데이터로 구성되어 있었습니다.  
따라서 위와 같은 MQL 데이터를 이용하여 고객의 영업 전환 여부를 예측하는 Task입니다.  
EDA 결과 유의미한 정보는 먼저 타겟값인 전환 여부의 데이터 불균형이 약 1(전환O) : 11(전환X)으로 심했습니다.  
추가적으로 데이터의 대부분이 0이나 NaN의 특성을 가진 Sparse 데이터셋의 특징을 띕니다.  
따라서 이 두가지의 특징은 학습시 방해요소로 작용합니다.  
또한 대부분 카테고리컬 피처가 많았고 이러한 피처들은 High Cardinality 특성을 띄고 있었습니다.  
따라서 피처 엔지니어링을 통해 Sparse Data 특징과 High Cardinality 특징을 어느 정도 순화해주었습니다.  

위와 같은 특징으로 고려할 사항으로는 Data Drift 가 있음을 배경지식을 이용하여 생각하였습니다.  

Data Drift란 모델 훈련시 입력 데이터의 통계적 분포 및 unique value와 테스트 시 실제 배포 환경에서의 입력 데이터의 통계적 분포 및 unique value가 어떠한 변화에 의해 차이가 발생하고 있는 것을 의미합니다. 

따라서 기존 고객 풀의 리드에 대한 영업전환예측의 경우, 높은 성공 예측률을 기록할 것이며, 
새로운 고객 또는 신규 영업사원으로 구성된 리드의 경우, 예측 성공률이 급격히 저하될 것으로 예측됩니다. 

따라서 저희는 모델의 예측에 어떤 피처가 제일 중요한지 확인하기 위해 Feature Importance 및 Shapely Value를 확인하였고 
특정 Customer_idx 및 Lead_owner가 모델 학습에 편향성을 가중시키고 있는 모습을 확인하였습니다.  

따라서 Customer_idx 및 Lead_owner가 결측치인 Lead에 대한 예측력이 현저히 떨어질 것으로 생각하였습니다.  

따라서 저희는 Modeling Based on CONTEXT 기법을 도입하였습니다.

이 기법은 4개의 모델을 활용합니다.
Model 1
 - Train 데이터의 customer_idx, lead_owner피처를 포함하여 학습시키고 Train에 존재하는 customer_idx, lead_owner unique 값으로 이루어진 Test 셋을 예측하는 모델

Model 2
 - Train 데이터의 customer_idx 피처를 포함시키고 lead_owner피처를 드랍하여 학습시키고 Train에 존재하는 customer_idx unique 값으로 이루어진 Test 셋을 예측하는 모델

Model 3
 - Train 데이터의 lead_owner 피처를 포함시키고 customer_idx피처를 드랍하여 학습시키고 Train에 존재하는 lead_owner unique 값으로 이루어진 Test 셋을 예측하는 모델

Model 4
 - Train 데이터의 lead_owner, customer_idx피처를 드랍하여 학습시키고 Train에 존재하지 않는 lead_owner unique 값으로 이루어진 Test 셋을 예측하는 모델

 
이 모델링 기법은 IF 문을 통해 들어온 데이터를 ISIN 메소드를 활용하여 바로 바로 필터링하여 원하는 모델로 보낼 수 있기 때문에 현업에서도 효과적이라 생각합니다.

그리고 제일 중요한 customer_idx, lead_owner 두 피처에 대한 Overfitting을 방지합니다.

따라서 저희의 모델은 MQL 데이터의 특성에 기반한 모델링 기법을 제시하였다고 생각합니다.

이번 대회를 통해 정형 데이터를 자연어로도 접근할 수 있는 방법론에 대해 알게 되었고,
언더 샘플링을 통해 레이블간 비율을 1:1 로 맞추고 데이터셋트를 늘리는 방법도 배웠습니다. (ex / 불균형이 11:1이라면 1인 데이터 샘플들은 그대로 두고 11에 속하는 데이터 샘플들은 11개로 나누어서 총 11개의 데이터셋트를 만들어서 모델링)
오버피팅을 방지하는 여러 생각들을 기획하며 구현하는 좋은 경험이었다고 생각합니다.



... 


---


## 🎈 Modeling

**Predict Model**
```
Cat-Boost
```
