# House Price Prediction
- 세부 내용 및 코드는 House_Price_Regression.ipynb 에서 확인 가능
- URL: https://github.com/JSPARK113/House-Price-Prediction/blob/master/House_Price_Regression.ipynb

## 1. 프로젝트 개요
1. 내용: 주택의 정보(위치, 시설 상태 등)를 통해 가격 예측
2. 데이터 set: row 2919 x column 81 - 80개 변수, target은 Price
3. 평가 기준:  RMSE(Root-Mean-Squared-Error)
4. 수단: Ordinary Least Squares regression (OLS)
5. 최종 성적: 0.14826, 5112명 중 2509위

## 2. Preprocessing
1. target data: 예측해야하는 값인 주택 가격의 분포 확인
    - 오른쪽 꼬리가 긴 모양을 보임.
    - log를 취해 정규분포에 가깝게 데이터를 변환해 줌.
2. feature data 확인
    - info 함수 사용
    - https://goo.gl/19Qkf1 문서 참고
    - 실수값과 카테고리값이 섞여 있음
        - 카테고리 값 처리 필요
    - missing data 처리 필요
3. Null 값 처리: missing data는 train과 test data를 한꺼번에 처리
    - 카테고리 값 중에서 order가 있는 변수는 숫자로 바꿔서 회귀분석 진행
        - 그래프 상으로 봤을 때 등급이 올라가면 대체로 SalePrice가 올라감을 확인
    - null 값은 해당 항목에 대한 '시설'이 없는 것임으로 점수를 0으로 매겨줌.
    - nan 값이 '시설 없음'인 변수들에 대해서 'N' 이라는 클래스를 부여해 줌 : 카테고리 값
    - nan 값이 '시설 없음'인 변수들에 대해서 0으로 대체 : 실수 값
4. 함께 처리했던 train과 test 데이터를 다시 분리

## 3. EDA & Feature selection
1. Numeric data
    - 독립변수와 종속변수 간 상관계수 확인 - heatmap 확인
    - 상관계수가 높은 데이터의 plot 확인
    - 선택 변수
        - OverallQual, TotSF, GrLivArea, GarageCars, ExterQual, KitchenQual, GarageArea, BsmtQual, YearBuilt, YearRemodAdd, OverallCond
2. Categorical data
    - one-way ANOVA를 통해 p-value, R-square, adj. R-square 계산
    - 상관관계가 높은 변수 선택
    - 선택 변수
        - Neighborhood, MasVnrType, SaleType, MSZoning

## 4. 모델링
1. OLS Regression 모델 - 카테고리 값
    - 위에서 고른 feature 중 카테고리 값만으로 OLS 모델링
    - F-테스트를 통한 확률이 지나치게 높아 유의미하지 않다고 생각되는 변수 다시한번 제거
2. 카테고리 변수 간 다중공선성 확인
    - 상관계수가 높은 GrLivArea, TotSF 중 TotSF 제거
    - 다중공선성 확인을 위해 VIF 확인
3. OLS Regression 모델 확정
    - 위 과정에서 일부 feature 데이터 제거 후 최종 모델 확정
    - 모델링 결과
        - R-square: 0.866
        - Adj. R-square: 0.863
        - Condition No.: 96.1
4. 다항회귀
    - 다항식을 이용하면 더 나은 성능을 보이는 변수가 있는지 살펴봄.
    - 교차검증을 통해 성능이 좋은 모델 두가지를 선정
        - A 모델
            - R-square: 0.886
            - Adj. R-square: 0.883
            - Condition No.: 268
        - B 모델
            - R-square: 0.885
            - Adj. R-square: 0.882
            - Condition No.: 106
5. 레버리지를 통한 아웃라이어 제거
    - 레버리지를 확인해 아웃라이어 제거
    - 아웃라이어 제거 후 모델링 다시 진행
        - A 모델
            - R-square: 0.925
            - Adj. R-square: 0.923
            - Condition No.: 280
        - B 모델
            - R-square: 0.920
            - Adj. R-square: 0.918
            - Condition No.: 184
6. 잔차 정규성 확인

## 5. 예측 및 제출
- A 모델
    - RMSE = 0.14909
- B 모델
    - RMSE = 0.14826
