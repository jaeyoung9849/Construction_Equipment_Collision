# Construction_Equipment_Collision

## :four_leaf_clover: 소개
- A공장은 새로 도입한 장비의 지속적인 고장으로 인하여 **생산 스케줄이 지연**되고 있습니다.
- 장비의 특성상 **한 번 고장이 나면 라인 전체를 Stop 시켜야** 하므로 공정 Process에서 Bottleneck 구간입니다.  
- 고장이 나면 막대한 손해이기 때문에 **사전 이상징후를 포착하고 점검**을 통해 고장이 발생하여 미치는 손실을 줄이고자 합니다.

<br/>
<br/>

----------------------------

## :four_leaf_clover: 데이터
- **features 데이터** : 6개의 Column과 1,050,000개의 row
- **target 데이터** : 5개의 Column과 2,800개의 row
- features와 target을 **Id를 기준으로 merge**하여 분석했습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/bcf4911b-9b21-429c-9dc1-192868e5688a)

<br/>
<br/>

---------------------------

## :four_leaf_clover: 결론
- **Rule-base, Supervised, Unsupervised 3가지의 모델**을 구축한 결과 **Recall 측면에서 Rule-base모델(59%)이 가장 성능이 좋은 것**으로 나타났습니다.
- **Rule-base모델은 단순하지만** 복잡한 머신러닝 모델을 구축할 필요가 없기 때문에 **시간과 비용을 절약**할 수 있습니다.
- 데이터를 실시간으로 모니터링할 수 있는 인프라를 만들고 **Rule-base모델(± 10,000)을 구축하여 이상징후가 나타나면 점검**을 수행합니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/5a941d57-67cf-49af-b821-d44463f38b4c)

<br/>
<br/>

--------------------------

## :four_leaf_clover: 모델링
**기본가정** : **충돌에너지(M*V)의 평균(μ)에서 ± 2표준편차(σ) 이상 떨어진 데이터(약 6%)를 이상치로 설정**했습니다.

<br/>

### 1) Rule-base 모델
- 4개의 센서(S1 ~ S4) 모두 **± 10,000을 넘어가는 데이터를 이상치로 정의**했습니다.
- **4개의 센서 모두 정상범위(±10,000)안에서의 이상값이 약 3%이고, 정상범위(±10,000)밖에서의 이상값이 약 29%** 를 보였기 때문입니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/b730f77f-39d6-46ff-a6d3-1d80a5e8f432)

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/2d3764fe-0af6-4088-8161-f7ec5e59979e)


<br/>
<br/>
<br/>

- **센서 S4에서 Recall의 성능(59%)이 가장 좋은것** 으로 나타났습니다.

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/0da9dac7-5510-4d68-9ff9-6c4c9b97d6cf)

<br/>
<br/>
<br/>

### 2) RandomForestClassifier 모델
- Supervised 모델로 RandomForestClassifier(RFC) 모델을 사용했습니다.
- RFC 기본 모델을 구축하고 테스트 데이터로 성능을 측정한 결과 **Recall이 62%** 가 나왔습니다.
- 하지만 **학습용 데이터에 과적합(100%)** 되어 있어 모델의 하이퍼 파리미터를 튜닝할 필요가 있습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/6c7f0792-f68b-4c39-a372-e007a9c14be7)

<br/>
<br/>

- **GridSearch**를 이용하여 RFC 모델의 **하이퍼 파라미터를 튜닝**했습니다.
- RFC 기본 모델보다 **튜닝된 모델의 학습 데이터와 테스트 데이터의 성능의 격차(66% - 46% = 20%)가 줄어들었습니다.**
- 튜닝된 모델을 구축하고 테스트 데이터로 성능을 측정한 결과 **Recall이 46%** 가 나왔습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/188656c9-3ec9-451b-bbcf-2a928394ac18)

<br/>
<br/>

- RFC 모델을 통해 **변수 중요도를 파악한 결과 S4변수가 가장 중요**한 것으로 나타났습니다.

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/960333a2-a910-47d4-b152-bef6d821b9ff)

<br/>
<br/>

- **각 변수의 제곱과 변수끼리의 곱으로 파생 변수**를 생성했습니다.
- **S4변수의 제곱**과 **S1과 S2의 곱 변수**가 가장 중요한 것으로 나타났습니다.
- 추후에 중요한 것으로 나타난 파생변수를 가지고 모델을 재 학습시킬 수 있습니다.

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/fa00bff4-71b0-45ee-be22-17cd35f7c2ad)

<br/>
<br/>
<br/>

### 3) IsolationForest 모델
- Unsupervised 모델로 IsolationForest 모델을 사용했습니다.
- max_samples를 100, contamination을 0.06으로 설정했습니다.
- **Unsupervised 모델인데도 불구하고 Recall이 54%** 로 나타났습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/110557a8-f81e-4f3f-af66-45aeefd1371f)


<br/>
<br/>

--------------------------

## :four_leaf_clover: 전처리
- **M(질량)과 V(속도)를 곱해서 충돌에너지(M*V)** 를 만들었습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/659e251c-a029-4bc4-b312-8dd8afc4c13c)

<br/>

- 이상(abnormal)을 정의하기 위해 **충돌에너지(M*V)의 분포**를 확인했습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/4d658c84-4237-4464-b995-7ebcf20327a9)

<br/>

- **충돌에너지(M*V)의 평균에서 2표준편차 이상 차이**나는 데이터를 이상치로 정의했습니다.
- **약 6%의 데이터가 이상치**인 것으로 나타났습니다.

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/370f12ff-2c27-4ead-99dd-7f0ba3c021bc)


<br/>

- **센서별(S1 ~ S4)로 정상데이터와 이상데이터의 분포**를 확인했습니다.
- **S1 ~ S4 모두 이상데이터에서 더 큰 분산**을 보였습니다.

<br/>

![image](https://github.com/jaeyoung9849/Construction_Equipment_Collision/assets/56102116/46452488-10ab-4641-87fa-0238c7ab9196)

<br/>
<br/>

------------------------------

## :four_leaf_clover: 한계점

- 이상 데이터를 정의하는 과정에서 **주관적인 판단**이 들어갔습니다.
- 실제 업무에 적용할 때는 **유관부서, 현업 엔지니어 등** 의 의견을 반영하여 좀 더 **객관적인 기준** 마련이 필요합니다.


<br/>
<br/>


