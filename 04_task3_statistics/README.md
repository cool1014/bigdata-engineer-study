# 빅데이터분석기사 실기 - 3유형 통계 검정

---

## 1. F-검정 (F-test)

**목적**: 두 집단의 분산이 같은지 검정

$$F = \frac{S_1^2}{S_2^2}$$

- $S_1^2$: 첫 번째 집단의 표본분산
- $S_2^2$: 두 번째 집단의 표본분산
- 일반적으로 큰 분산을 분자에 두어 $F \geq 1$이 되도록 함

```python
from scipy.stats import levene, bartlett

# Levene 검정 (정규성 가정 불필요, 더 범용적)
stat, p = levene(group1, group2)

# Bartlett 검정 (정규분포 가정)
stat, p = bartlett(group1, group2)

print(f"통계량: {stat}, p-value: {p}")
# p > 0.05 → 등분산 (분산이 같다)
# p < 0.05 → 이분산 (분산이 다르다)
```

---

## 2. 합동 분산 추정량 (Pooled Variance)

**목적**: 두 집단의 분산이 같다고 가정할 때, 두 집단을 합쳐서 분산을 하나로 추정

$$S_p^2 = \frac{(n_1 - 1)S_1^2 + (n_2 - 1)S_2^2}{n_1 + n_2 - 2}$$

- 등분산 가정이 성립할 때 t-검정에서 사용
- 각 집단의 표본 크기로 가중 평균한 분산

---

## 3. t-검정 (t-test)

**목적**: 두 집단의 평균이 같은지 검정

| 종류            | 설명                         | 함수          |
| --------------- | ---------------------------- | ------------- |
| 독립표본 t-검정 | 서로 다른 두 집단 비교       | `ttest_ind`   |
| 대응표본 t-검정 | 같은 집단의 전/후 비교       | `ttest_rel`   |
| 단일표본 t-검정 | 하나의 집단을 특정 값과 비교 | `ttest_1samp` |

```python
from scipy.stats import ttest_ind, ttest_rel, ttest_1samp

# 독립표본 (등분산 O → equal_var=True, 이분산 → equal_var=False)
stat, p = ttest_ind(group1, group2, equal_var=True)

# 대응표본
stat, p = ttest_rel(before, after)

# 단일표본
stat, p = ttest_1samp(data, popmean=50)

print(f"통계량: {stat}, p-value: {p}")
# p < 0.05 → 평균 차이 있음 (귀무가설 기각)
```

> **흐름**: F-검정으로 등분산 여부 확인 → t-검정 `equal_var` 설정

---

## 4. 다중선형 회귀분석 (Multiple Linear Regression)

**목적**: 여러 독립변수로 연속형 종속변수 예측

```python
from statsmodels.api import OLS

# formula 방식 (문자열 변수 자동 더미 처리, 인코딩 불필요)
formula = "타겟 ~ 변수1 + 변수2 + 변수3"
model = OLS.from_formula(formula, df).fit()
print(model.summary())

# 특정 변수 계수
print(round(model.params['변수1'], 3))
```

**결과 해석**

| 항목        | 의미                                          |
| ----------- | --------------------------------------------- |
| coef        | 회귀계수 (독립변수 1 증가 시 종속변수 변화량) |
| P>\|t\|     | p-value (0.05 미만이면 유의미한 변수)         |
| R-squared   | 모델 설명력 (1에 가까울수록 좋음)             |
| F-statistic | 모델 전체의 유의성                            |

---

## 5. 로지스틱 회귀분석 (Logistic Regression)

**목적**: 이진 분류 (0/1) 예측, 확률로 결과 반환

```python
import numpy as np
from statsmodels.api import Logit

# formula 방식 (문자열 변수 자동 더미 처리, 인코딩 불필요)
formula = "타겟 ~ 변수1 + 변수2 + 변수3"
model = Logit.from_formula(formula, df).fit()
print(model.summary())

# 특정 변수 계수
print(round(model.params['변수1'], 3))

# 특정 변수 오즈비 (한 단위 증가 시)
print(round(np.exp(model.params['변수1']), 3))
```

**결과 해석**

| 항목           | 의미                                           |
| -------------- | ---------------------------------------------- |
| coef           | 로그 오즈비 (양수: 확률 증가, 음수: 확률 감소) |
| P>\|z\|        | p-value (0.05 미만이면 유의미)                 |
| `np.exp(coef)` | 오즈비 (1보다 크면 양의 영향)                  |

---

## 6. 카이제곱 검정 (Chi-Square Test)

**목적**: 범주형 변수 간의 독립성(관련성) 검정

```python
from scipy.stats import chi2_contingency
import pandas as pd

# 교차표 생성
ct = pd.crosstab(df['변수A'], df['변수B'])

stat, p, dof, expected = chi2_contingency(ct)
print(f"카이제곱 통계량: {stat}")
print(f"p-value: {p}")
print(f"자유도: {dof}")
# p < 0.05 → 두 변수는 관련 있음 (독립이 아님)
# p > 0.05 → 두 변수는 관련 없음 (독립)
```

---

## 빠른 판단 기준 요약

| p-value  | 결론                                      |
| -------- | ----------------------------------------- |
| p < 0.05 | 귀무가설 기각 → 유의미한 차이/관련성 있음 |
| p ≥ 0.05 | 귀무가설 채택 → 차이/관련성 없음          |

## 검정 선택 가이드

| 상황                  | 검정                        |
| --------------------- | --------------------------- |
| 두 집단 분산 비교     | F-검정 (levene)             |
| 두 집단 평균 비교     | t-검정 (ttest_ind)          |
| 전후 비교 (같은 집단) | 대응표본 t-검정 (ttest_rel) |
| 연속형 변수 예측      | 다중선형 회귀분석           |
| 이진 분류 예측        | 로지스틱 회귀분석           |
| 범주형 변수 간 관계   | 카이제곱 검정               |
