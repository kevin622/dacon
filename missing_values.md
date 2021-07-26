# missing value가 있을 경우 채워야 할까요? 그 이유는 무엇인가요?

크게 3가지 선택지가 존재함

1. 없앤다
2. 놔둔다
3. 채운다

## 없애는 경우

- 극소수의 데이터만 결측치일 경우

- 결측치 데이터를 '방해'라고 여길 수 있을 정도로 무시할 수 있는 경우

=> 주로 데이터의 행(row, 케이스) 자체를 지움

- 위험성 : '극소수' 개념이 주관적이고 모호함, 중요한 데이터를 삭제할 위험이 있음

## 놔두는 경우

- 결측치를 알아서 처리하는 머신러닝 알고리즘들도 존재! xgboost 이 대표적
- 하지만 대부분 잘 안 되어 있음...놔두지는 말자

> 참고: 결측치 자체가 의미가 있을 수 있다!

국민투표에 다수가 참여하지 않아 그들의 투표 결과가 결측치라면?

=> ''그들의 성향을 알 수 없음''이라고 결론 내리거나 결측치를 삭제해서 "그들의 성향을 무시"하는 것이 아니라, 투표를 하지 않은 원인을 탐색하는 출발점으로 삼아야 함

=> 데이터 관점으로 말하자면, '투표 여부'를 하나의 피쳐(새로운 열)로 추가하고 새로운 분석 시도 필요

## 채우는 경우

방법론이 많음! 결측치 처리를 전공으로 하는 교수들도 있을 정도로 공부할 게 많은 분야
자세히 보지는 말고 대충만 알아봅시다

> 참고: 결측치를 '잘' 채운 기준은 데이터의 품질! 데이터의 품질이라는 개념은 명확한 정의가 없지만, 데이터로부터 얻을 수 있는 정보가 적어질수록, 왜곡될수록 품질이 떨어진다고 본다

### single value imputation

해당 데이터의 평균/중앙값/최빈값/0/특정상수 등으로 결측치를 모두 채우는 방법

쉬우 방법이지만 상관관계, 분포 등 고려할 수 있는 것들을 다 무시한 방법이기에 데이터 품질이 떨어짐

```python
# 데이터는 pandas df 형태이다.

# 변환 전 데이터 확인
df_null.head(10)

#변환
from sklearn.impute import SimpleImputer
imp_mean = SimpleImputer( strategy='most_frequent') #'median'을 쓰면 중앙값사용
imp_mean.fit(df_null)
df_imputed = pd.DataFrame(imp_mean.transform(df_null))

# 변환 후 데이터 확인
df_imputed.head(10)
```



### KNN imputation

결측치 이외의 feature들을 사용해 가장 유사도가 높은 데이터의 값을 사용

```python
# 변환 전 데이터 확인
df_null.head(10)

#변환
from impyute.imputation.cs import fast_knn
np_imputed=fast_knn(df_null.values, k=5)# KNN 학습 
df_imputed = pd.DataFrame(np_imputed)

# 변환 후 데이터 확인
df_imputed.head(10)
```

이상치에 민감하다는 단점
데이터 전체를 메모리에 올려야 함

### MICE(Multiple Imputation by Chained Equations)

여기부터는 자세히 들어가면 좀 어려움...

분포를 가정하고 (결측치를 채울) 다수의 데이터를 생성한 후 데이터를 기반으로 분포에 대한 가정을 업데이트하고, 새로운 분포 가정을 토대로 다시 데이터를 생성하는 방법 (베이즈 정리에 기반한 방법! 베이지언들이 많이 사용한다고 하네요)

확실치 않아서...다수의 데이터를 생성해서 사용한다는 것만 기억합시다.

```python
# 변환 전 데이터 확인
df_null.head(10)

#변환
from impyute.imputation.cs import mice
np_imputed=mice(df_null.values) # mice 학습시작
df_imputed = pd.DataFrame(np_imputed)

# 변환 후 데이터 확인
df_imputed.head(10)
```

> 참고 : datawig라는 라이브러리를 사용하면 딥러닝 모델을 이용해 결측치들의 예측값을 구할 수도 있는데, 말 그대로 그냥 딥러닝 모델 돌리는 거라 더 설명 안 합니다.



## 결론

레퍼런스 중 한 곳의 문단을 그대로 가져옴

_결론적으로 **누락된 값을 대체하는 완벽한 방법은 없다. 각각의 방법은 데이터 유형에 따라 더 나은 성능을 나타내기도 하고 더 나쁜 성능을 발휘하기도 한다.** 한 피쳐에 누락된 데이터가 너무 많다면 해당 피쳐를 없애기도 하고, 어떤 때에는 피쳐는 상수값으로 대체하기도 한다. missing data를 역 추산하는 시간에 너무 큰 시간을 들여서 모델을 한 번도 못 만들어보고 김이 샐 수도 있으니 적당히 빠른 것으로 시도한 뒤 하나씩 대체해나가는 것도 좋은 방법이다. 나중에 설명된 방법들이 더 좋은 방법이라고 생각할 수도 있지만, 상황에 따라 좋은 방법이 다르다. 어떤 것들을 사용해야 할지 규칙도 있지만, 결국 어떤 게 가장 적합한지는 모델을 통해 실험하고 확인해야 한다. 그때그때 적합한 방법을 사용하자._



https://dining-developer.tistory.com/19
https://towardsdatascience.com/6-different-ways-to-compensate-for-missing-values-data-imputation-with-examples-6022d9ca0779

