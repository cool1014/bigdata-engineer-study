# Chapter 02 - 정렬, 날짜 데이터, 중복 제거

### 1. 데이터 정렬하기

- **문법:** `데이터프레임.sort_values(by='정렬할 컬럼', ascending=True/False)`
  - `ascending=True` : 오름차순 (기본값)
  - `ascending=False` : 내림차순

> ⚠️ `True`, `False` 작성 시 **첫 글자를 반드시 대문자**로 써야 합니다.

```python
# 이름과 월급을 출력하는데 월급이 높은 사원부터 출력
emp[['ename', 'sal']].sort_values(by='sal', ascending=False)
```

---

### 2. 데이터프레임 구조 확인

```python
emp.info()
```

```
 0   empno     14 non-null     int64
 1   ename     14 non-null     object
 2   job       14 non-null     object
 3   mgr       13 non-null     float64
 4   hiredate  14 non-null     object      ← 문자형으로 불러와짐
 5   sal       14 non-null     int64
 6   comm      4 non-null      float64
 7   deptno    14 non-null     int64
```

---

### 3. 날짜 데이터 다루기

CSV 파일을 판다스로 불러오면 날짜 컬럼이 기본적으로 **문자형(object)** 으로 인식됩니다.
날짜 데이터를 조건 검색하려면 **날짜형(datetime)으로 변환**이 필요합니다.

```python
import pandas as pd

# hiredate 컬럼을 날짜형으로 변환 후 반영
emp['hiredate'] = pd.to_datetime(emp['hiredate'])

emp.info()
# hiredate  14 non-null  datetime64[ns]  ← 날짜형으로 변환됨
```

#### 💻 활용 예시

```python
# 직업이 SALESMAN이고 1981년도에 입사한 사원의 이름, 직업, 입사일 출력
emp = pd.read_csv('/content/drive/MyDrive/data100/emp.csv')

result = emp[['ename', 'job', 'hiredate']][emp['hiredate'].between('1981-01-01', '1981-12-31')]
result[:][result['job'] == 'SALESMAN']
```

---

### 4. 중복 데이터 제거

- **문법:** `시리즈.unique()`

> ⚠️ `unique()`는 **시리즈 전용 함수**입니다. 대괄호를 2개 쓰면 데이터프레임이 되어 에러가 납니다.

```python
# 부서번호를 중복 제거해서 출력
emp['deptno'].unique()
```

#### 📌 DataFrame vs Series

| 문법           | 반환 타입          | unique() 사용 |
| -------------- | ------------------ | ------------- |
| `emp[['job']]` | DataFrame (테이블) | ❌ 에러       |
| `emp['job']`   | Series (컬럼)      | ✅ 가능       |

```python
emp['job'][emp['deptno'] == 20].unique()    # ✅ 정상
emp[['job']][emp['deptno'] == 20].unique()  # ❌ 에러
```

#### 💻 활용 예시

```python
# 통신사가 sk 또는 kt인 사원들의 나이(중복 제거)
emp20['age'][emp20.telecom.isin(['sk', 'kt'])].unique()
```
