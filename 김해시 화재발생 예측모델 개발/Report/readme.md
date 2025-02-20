# Leaderboard
![Score_board](https://user-images.githubusercontent.com/40457277/71368300-ea32e380-25ea-11ea-8183-eadea39ff594.png)

## 1. 개요
- 경상남도의 데이터를 이용하여 김해시의 화재발생 여부를 예측하는 문제. 
- Metric은 f1_Score로 1등은 0.59187 글쓴이는 0.50207로 9등을 차지하였다. (300팀이상 참여)
## 2. 데이터
- PJT002_train.csv (경상남도 지역의 날짜, 시간, 건물화재여부, 건물정보 등에 대한 정보를 담고있습니다. 무작위 샘플링된 데이터 입니다.) 
- PJT002_validation.csv (위와 동일하되, 김해시 지역으로 한정되어있습니다.)
- PJT002_test.csv	(위와 동일하되, 김해시 지역으로 한정되어있습니다.예측해야하는 fr_yn 이 없습니다.)
## 3. 데이터 EDA + 모델링
1) 건물특징 feature가 대부분 이였고, 소방시설 관련 feature, 날씨 feature 등이 있었다.
2) 클렌징이 전혀안된 공공데이터이다보니 결측치,이상치가 굉장히 많았다.(공공데이터 프로젝트를 2번 해보왔으나 항상 데이터가 이상하였다. 개인적인 생각으로는 데이터 수집이나 입력 과정에서 불성실하게 저장되는거 같다.) 그래서 데이터 eda를 통한 결측치 제거 및 보간, skew기준으로 이상치 제거와 같은 통계적 기법으로 데이터 전처리를 하였으나 좋은 성능이 나오지 않았다. 그 이유는 test셋 역시 결측치 및 이상치가 많은 데이터라고 판단하였다. 
3) 그리하여 EDA와 도메인 지식없이 feature의 특징만으로 feature engineering을 진행하였다.  
    - feature의 결측치가 60%이상이거나, string + float가 섞여있는 feature는 가감히 제거하였다.
    - value_counts()의 threshold에 따라 category feature라 생각하는 feature는 최빈값으로 대체하였다. 그 후 원핫인코딩을 진행하였다.
    - 나머지 feature는 continuous feature라 가정하고, 선형 보간 및 절사평균으로 대체하였다.
4) 모델은 sklearn 패키지 모델, boosting계열, keras 모델 30개 정도 모델을 적용 시켜보았고 xgboost + lgbm boosting 앙상블 모델이 가장 성능이 좋았다.
## 4. 회고
1) class imblanced가 어느정도 있었고 이럴때마다 resampling을 적용하지만 train셋 성능만 올라가지 tes셋 성능은 항상 좋지않았다. 실제 데이터가 아닌 생성된 데이터이기 때문에 이런 결과가 도출되는거 같다.(이론과 현실의 차이)
2) 위의 문제를 해결하기 위해 좋은 데이터를 선별하여 학습시키는 방법이 있는데 이게 많은 시간과 노력이 들어가서 쉽지않다.(참가 기간이 길었다면 좋았을듯.)
3) 요번 프로젝트를 통해 여러가지 target encoding에 학습하였다. 하지만 target encoding 자체가 클래스의 성격이 포함되기 때문에 test셋에서는 드라마틱한 성능을 내지는 못하는듯하다.(스무딩이나 kfold을 이용한 인코딩도 그닥 좋은 성능을 내진 못했다.)
4) gridsearch,kfold 기법이 시간에 비해 좋은 성능을 내진 못하는거 같다. 오히려 feature engineering을 한개 더 하는게 시간상 효율적이다.
5) predict_proba에 threshold를 조절하여 예측을 확률을 변경하는 기법을 시도해 보왔으니 좋은 성능을 내진 못했다.
6) kaggle과 같은 submission 제출 과제는 실제 test셋과 비슷한 validation셋 구축이 제일 중요한거 같다. 하지만 이 방법이 쉽지않다. ***
