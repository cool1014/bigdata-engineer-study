# 빅데이터분석기사 실기 - 2유형 템플릿

## 공통 전처리 흐름

```python
import pandas as pd

train = pd.read_csv("data/customer_train.csv")
test  = pd.read_csv("data/customer_test.csv")

# 1. X, y, 전체 X 분리
X = train.drop(['타겟컬럼'], axis=1)
y = train['타겟컬럼']
X_full = pd.concat([X, test], axis=0)
X_full = X_full.drop(['고유번호컬럼'], axis=1)   # 불필요 ID 컬럼 제거

# 2. 결측치 처리
X_full['결측컬럼'] = X_full['결측컬럼'].fillna(0)

# 3. 범주형 인코딩
X_full = pd.get_dummies(X_full)

# 4. train / test 재분리
X_train = X_full[:train.shape[0]]
X_test  = X_full[train.shape[0]:]

from sklearn.model_selection import train_test_split
X_train, X_val, y_train, y_val = train_test_split(X_train, y, test_size=0.2)
```

---

## 🔵 회귀 (Regression)

```python
from sklearn.ensemble import RandomForestRegressor

model = RandomForestRegressor()
model.fit(X_train, y_train)
y_pred_val = model.predict(X_val)

from sklearn.metrics import root_mean_squared_error, r2_score

rmse = root_mean_squared_error(y_val, y_pred_val)
r2   = r2_score(y_val, y_pred_val)
print(rmse, r2)

# 결과 저장
y_pred = model.predict(X_test)
pd.DataFrame(y_pred, columns=['pred']).to_csv('result.csv', index=False)
```

| 평가지표 | 함수                                     |
| -------- | ---------------------------------------- |
| RMSE     | `root_mean_squared_error(y_val, y_pred)` |
| R²       | `r2_score(y_val, y_pred)`                |

---

## 🟢 분류 (Classification)

```python
from sklearn.ensemble import RandomForestClassifier

model = RandomForestClassifier()
model.fit(X_train, y_train)
y_pred_val = model.predict(X_val)

from sklearn.metrics import roc_auc_score, accuracy_score

auc_score = roc_auc_score(y_val, y_val_pred)
acc = accuracy_score(y_val, y_val_pred)
print(auc_score, acc)

# 결과 저장
y_pred = model.predict(X_test)
pd.DataFrame(y_pred, columns=['pred']).to_csv('result.csv', index=False)
```

| 평가지표 | 함수                            |
| -------- | ------------------------------- |
| 정확도   | `accuracy_score(y_val, y_pred)` |
| AUC      | `roc_auc_score(y_val, y_pred)`  |

---

## 핵심 체크리스트

- [ ] 타겟 컬럼명 확인 후 `drop` 수정
- [ ] ID 컬럼 존재 여부 확인 후 `drop` 수정
- [ ] 결측치 컬럼 확인 (`fillna` 전략 결정)
- [ ] 회귀 → `RandomForestRegressor` / 분류 → `RandomForestClassifier`
- [ ] 저장 파일명 `result.csv`, 컬럼명 `pred` 확인
