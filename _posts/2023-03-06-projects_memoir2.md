---
title: "[프로젝트회고록] 02. 두번째 회고록: 유의차 분석"

categories:
    - Data
tags:
    - Data
    - Projects
    - Memoir
last_modified_at: 2023-03-06T22:43:00

toc: true
toc_sticky: true
toc_label: "On This Page"
---

&#160; 안녕하세요. 이번에는 Y인자가 **이항 변수(Binary Variable)**일 때, 어떻게 문제에 접근했고 풀었는지 초점을 맞춰서 프로젝트를 회고해보고자 합니다. 주 포인트는 아래와 같습니다.
> 1. 유의차 분석
> 2. 데이터 셋이 불균형일 때 해결방법

## 1. 프로젝트 개요
&#160; 이번 프로젝트에서 클라이언트의 요청사항은 2가지 였다. 첫 번째는 특정 공정에서 발생하는 품질 이상 발생 방지를 위하여 핵심인자를 규명하는 것이었고, 두 번쨰로는 향후 
생산속도 증속 대비 최적 공정조건을 설정하기 위한 시뮬레이션을 위한 품질 이상 발생 확률을 예측하는 예측모형 개발이었다. 

### 1.1 문제 정의
&#160; 우리는 문제를 풀기 위해 Y인자를 품질 이상 발생/미발생 제품을 나타내는 이항 변수(Binary Variable)로 정의했다. 당시 클라이언트는 제품별로 품질 이상 발생 이력을 데이터화하여 관리하고 있었기에
해당 데이터를 Y인자로 활용했고, 관련된 모든 데이터를 모아 분석을 위한 데이터 마트를 구성했다.

### 1.2 가설 설정 & 분석 전략 수립
&#160; 품질 이상 발생 여부를 기준으로 우리는 제품을 두 그룹으로 나눴고, 유의차 분석 방법을 적용해 핵심인자를 규명하기로 했다. 우리의 가설은 품질에 영향을 미치는 핵심인자라면,
 양 그룹 간 평균 혹은 분포 등 차이가 있을 것이라는 점이었다. 우리는 탐색적 자료 분석의 단계를 아래와 같이 두 단계로 놔눴고, 하나하나 잠재 인자들을 탐색하기로 했다.
> 1. t-test 검정을 통해 양 그룹 간 평균의 차이가 있는 인자들을 탐색한 후,
> 2. 라인차트, 산점도, 박스플롯 등을 활용하여 후보인자의 그룹 간 차이에 대해서 엄밀하게 검증함

통계 검정은 만능이 아니기에 통계 검정만으로 결론을 내기에는 부족하고 2번의 과정 역시 매우 중요하다. 실제로 분석하다보면, 통계적 검정이 통계적으로 유의미한 결과를 보이는 경우에도 데이터를 시각적으로 확인해보면 
아닌 경우가 매우 많다.

### 1.3 예측 모델링
&#160; 설명 가능한 예측모형을 고려하여 우리는 로짓회귀 모형을 선택해 예측 모형을 개발하기로 했다. 유의차 분석 결과를 통해 도출한 핵심인자와 기본 생산 조건을 나타내는 변수들을 조합하여 회귀식을 세웠다. 보통 Y인자가 이항 변수일 때, 예측 모형 평가지표는 정확도, AUC 등을 활용한다. 하지만 우리의 목표는 불량 제품을 제대로 예측하는 것이었기에 불량 정확도 역시 중요 평가지표로 활용해 예측 모형의 예측력을 평가했다. 

예측 모형을 개발하기 위해 풀어야 했던 문제 중 하나는 데이터 셋의 불균형이었다. 당시 데이터 셋에서 불량 제품의 비중이 5%도 안될 정도로 불균형이 심각했다.
원자료셋을 그대로 사용했을 경우 예측 모형의 예측력이 좋지 않았기에 예측력을 향상시키기 위해서 고민했고, 샘플링 기법을 활용해 예측력을 보완하고자 했다.

### 1.4 결과 
&#160; 샘플링 기법을 활용해 다양한 데이터 셋을 만들어서 학습하여 예측력을 비교해 최종 모형을 선택했고, 주요 인자의 값에 따라 불량 제품 확률을 시뮬레이션하여
클라이언트에게 공유했다. 솔직하게 말하자면 ML모형의 예측력은 아주 떨어지는 것은 아니었으나 예측 결과에 대한 클라이언트의 결론은 실제 현장에 적용하기 어렵다는 것이었다. 이유는 예측 모형의 포함된 한 변수에 대해서 계수의 부호가 반대로 나왔기 때문이다. 참고용으로만 활용됐고 ML모형을 보완하거나 후속 작업이 이뤄지진 않았다.
어떠한 조건에서 불량 제품이 발생할 확률이 높아지는지 확인할 수 있어서 소기의 성과를 올렸기에 해당 프로젝트는 종료하고 다음 프로젝트로 넘어갔다.
<br>

## 2. 새로 배운 것

&#160; 이번 프로젝트는 주니어 데이터 분석가로서 많은 것을 배울 수 있던 프로젝트였다. 특히, 불균형 데이터 셋 하에서 유의차 분석과 예측 모형 개발에 대해서
심도있게 고민해볼 수 있는 좋은 기회였다.

### 2.1 불균형 데이터 셋
&#160; 학교에서 공부할 때에는 불균형이 심한 데이터 셋을 다뤘던 경험이 없었기에 이번 프로젝트가 처음으로 불균형햔 데이터 셋을 다룬 첫 번째 프로젝트였다.
당시 데이터 셋에서 정상/불량 제품이 대략 900/15 정도 였던 걸로 기억한다(정확하지 않다). 분석을 진행해보니 데이터 셋의 불균형이 심할 경우, 2가지 관점에서
문제가 발생했다.
> 1. t-test 검정 결과의 신뢰도 하락
> 2. ML모형의 예측력 하락

#### 2.1.1 t-test 검정
&#160; 불균형이 심할 때, 인자의 양 그룹 별 평균 차이를 검정하면 어떻게 될까!? 대부분의 인자의 t-test 검정에서 5% 유의수준에서 귀무가설을 기각하는 결과가 나왔다. 즉, 웬만하면 통계적으로 양 그룹 간 평균의 차이가 있다는 것이다. 따라서 t-test 검정을 통한 유의차 분석 만으로는 유의미한 결론을 도출하기 어려웠다. <br><br>
왜 그럴까? 다음과 같은 간단한 실험을 해보자.
<br><br>
&#160; 우선 실험을 위해 임의의 100개의 X인자 데이터를 만들었다. 0 ~ 1 범위 내에서 10개의 임의의 난수를 생성해 불량 그룹 데이터를 만들고, 마찬가지로 0 ~ 1 범위 내에서 35개와 1 ~ 2 범위 내에서 45개 총 90개의 임의의 난수를 생성해 정상 그룹 데이터를 생성했다. 그룹 별 데이터의 평균값은 다음과 같다.

|그룹|평균|
|---|---|
|불량|0.33|
|정상|1.05|

&#160; 그룹 간 X인자의 평균 차이는 대략 0.7이며, 평균 차이에 대한 t-test 검정 결과 p-value = 0.0002로 통계적으로 매우 유의미한 평균 차이가 존재한다는 결과가 나왔다. t-test 검정 결과만 보면, 이 X인자의 값이 낮을 때, 불량 제품 유발 확률이 높다고 보여진다. 하지만 정말 이 수치만을 신뢰하여 이런 결론을 도출해도 될까? 이 데이터를 시각적으로 간단하게 표현해봤다.
<div>
{% include test.html %}
</div><br>
&#160; 그래프를 확인해보면 전혀 그렇지 않다는 것을 알 수 있다. 이 X인자의 변동만으로는 불량 제품 발생에 대해서 전혀 설명하지 못하고 있다. 물론 실험을 위해 임의로 만든 데이터 셋이긴 하지만 프로젝트를 진행하면서 실제로 자주 마주쳤던 패턴이다. 제대로 교육받고 훈련된 데이터 분석가라면 t-test 검정 결과가 만능이 아니라는 것은 누구나 다 알 것이다. 하지만 수백 ~ 수천개의 인자들을 하나하나 시각적으로 탐색하기에는 프로젝트 수행기간이 항상 넉넉하지 않기에 이런 통계 검정, 통계 수치에 의존하여 인자들을 필터링하며 데이터 분석을 진행하게 된다. 중요한 점은 통계 검정 수치만 전적으로 신뢰하는 것이 아니라 다양한 방면으로 분석하고 검증해야 한다는 것이다.

#### 2.1.2 샘플링
&#160; 불균형 데이터 셋을 학습했을 때 ML모형의 예측력이 좋지 않다는 것은 이미 널리 알려진 사실이지만 실제로 문제를 인식하고 배울 수 있던 것은 이번 프로젝트에서였다. 불균형 문제를 해결하기 위해서 팀장님은 랜덤 샘플링을 통한 해결 방법을 제시했고 이에 더하여 또 좋은 것들이 있는지 탐색했고 다음 방법을 통해 불균형을 해결하고자 했다.
> 1. Random Sampling[Over, Under, Hybrid(Over + Under)]
> 2. SMOTE Algorithm

&#160; 첫 번째 방법은 랜덤 샘플링이다. 랜덤 복원추출을 통해 크기가 큰 데이터 그룹의 수는 줄이고, 적은 그룹의 수는 늘려 불균형을 보정하는 방식이다. 장점은 적용하기 쉽지만, 단점은 크기가 작은 데이터 그룹의 데이터가 단순 복제된다는 점이다. SMOTE 알고리즘은 그룹 내에 데이터 간의 선형 결합을 통해 임의의 데이터를 생성하는 방식이다. 단순 복제하는 랜던 샘플링 방식에 비해 새로운 데이터가 생성된다는 점과 이해하기 쉽다는 장점이 있지만, 반대로 가상의 데이터를 임의로 생성한다는 단점이 있다. 하지만, 데이터를 생성하는 방식이 받아들일만 하다고 생각했고 적용하여 예측력을 검토해보고자 했다. <br>
&#160; 실제로 각 방법을 적용하여 불균형을 보정하고 ML모형의 예측력을 비교했을때, Random Sampling(Hybrid)와 SMOTE 알고리즘을 적용한 데이터 셋의 예측력이 향상된 것을 확인할 수 있었다. 단순한 방법으로도 ML모형의 예측력이 증가할 수 있다는 점이 흥미로웠다. ML모형이 데이터를 학습하는 것을 생각해보면 충분히 납득할만하다고 생각한다. 데이터 셋의 불균형이 심하다면 ML모형이 데이터의 수가 많은 그룹을 위주로 학습하기 때문이다. (이 말이 이론적으로 엄밀히 맞는 말인지는 모르겠다.) 프로젝트 중에는 시도해보지는 못했지만 회고록을 쓰고 있는 지금 시점에서 생각해보니 더 디테일하게 샘플링 방법을 설정하여 적용해보고 결과를 비교하는 것도 재밌는 작업이 될 것 같다. <br>
&#160; 한가지 조심해야 할 점은 과도한 보정이 들어갈 경우, 이 작업이 데이터 조작이 될 수도 있다는 점이다. 랜덤 샘플링의 경우 문제가 덜 하겠지만, 가상의 데이터를 생성하는 알고리즘(예: SMOTE)을 적용할 때는 항상 조작이 되지 않도록 유의하여 적용해야 할 것이다.

## 3. 아쉬운 점
&#160; 개인적으로 아쉬웠던 점은 고생해서 개발한 ML예측 모형이 클라이언트에게 큰 도움이 되지 않았다는 점이다. 당시, 클라이언트가 준 피드백 중 하나는 **"특정 인자의 부호가 이론적으로 맞지 않는다"** 였다. 하지만 그 인자는 중요한 외생 조건 변수라 회귀식에서 제거할 수 없었다. 그리고, 부호가 반대로 나온 이유도 사실 명확했다. (문제가 된 변수를 A변수라 해보자)
> A인자는 시간이 지남에 따라 계속 **증가**해왔고, 불량률은 시간이 지남에 따라 지속적으로 **감소**해왔다. 

&#160; 시간이 지남에 따라 위 두 변수가 맞물려 마치 A인자가 증가하면 불량률이 감소할 것이라는 결과가 도출된 것이다. 데이터의 수가 충분했다면 A인자의 값에 따라 데이터 셋을 나누면(층별화) 될 것이다. 하지만 그 당시, 데이터가 충분하지 않았기에 데이터 셋을 더 작게 나눌 수 없는 한계점이 존재했다. 클라이언트는 핵심인자과 그 매커니즘을 규정한 것으로도 만족했고, 이 결과를 활용해 충분히 의사결정이 가능하다고 하여 추가 보완을 진행하지 않았다. 필요하다면 추후 데이터의 수가 충분해졌을 때, 다시 시도해보고자 하고 이번 프로젝트는 종료됐다.

## 4. 마무리하며
&#160; 이번 프로젝트는 실무에서 처음으로 유의차 분석과 예측 모형을 개발하여 적용한 프로젝트였기에 배운게 많은 프로젝트로 기억에 남는다. 개인적으로는 로짓 모형 말고 다른 분류 모형을 학습했을 때와 데이터의 수가 훨씬 많았다면 결과가 어떻게 달라졌을까 하는 궁금증도 많이 남는 프로젝트였다.