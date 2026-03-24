# 월간 데이콘 항공편 지연 예측 AI 경진대회
<img width="944" height="220" alt="image" src="https://github.com/user-attachments/assets/3fdca5ee-4e15-4481-87a1-b9d3abeb28aa" />


[DACON](https://dacon.io/competitions/official/236094/overview/description)

-------------------------------------------------------------------------------------

**참고**

데이터 파일 크기로 인해 구글 드라이브 링크를 첨부함
[Google Drive](https://drive.google.com/drive/folders/1ZbPHU7GnVw-s0XCwGh3VXi1Ny_0rRX_R?usp=drive_link)

<br>

## 1. 개요 및 목적
[주제]
일부 레이블만 주어진 학습 데이터셋을 이용한 항공편 지연 여부 예측

[목적]
준지도학습을 이용한 월간 항공편 지연 예측 AI 모델 개발

[설명]
레이블이 없는 데이터와 함께 항공편 지연 여부를 예측하는 AI 모델 개발

## 2. 기술 스택 (Tech Stack)
| 구분 | 기술 |
|------|------|
| Language | Python |
| Data Processing | Pandas, NumPy |
| Analysis | Scikit-learn |
| Visualization | Matplotlib, Seaborn |
| Environment | Google Colab |

-----------------------------------------------------

## 3. 분석 프로세스 (Workflow)

1.  **데이터 전처리 (Data Preprocessing)**
    * 원시 데이터를 HS4 코드별 월간 무역량으로 집계 및 시계열 매트릭스 변환.
    * 시계열 연속성 확보를 위한 선형 보간(Interpolation) 수행.

2.  **공행성 판별 (Statistical Testing)**
    * `Statsmodels`를 활용하여 전 품목 대상 **Granger Causality Test** 수행.
    * 통계적으로 유의미한 선후행 쌍(A → B)을 필터링하여 학습 데이터셋 구성.

3.  **특성 공학 (Feature Engineering)**
    * 선행 품목의 과거 데이터(1, 2, 3개월 Lag) 생성 및 파생 변수 추가.
    * 범주형 변수(HS4 코드) 지정으로 모델의 학습 효율 증대.

4.  **모델 학습 및 추론 (Model & Inference)**
    * **HistGradientBoostingRegressor** 모델 구축 및 학습.
    * 2025년 8월 타겟 품목의 무역량(Value) 최종 예측값 산출.

5.  **성과 평가 (Evaluation)**
    * 검증 데이터셋을 통한 에러 분석 및 시각화를 통한 최종 검토.

-----------------------------------------------------

## 4. 🚀 Key Results (핵심 성과)
### ✅ 결과 1. 통계적 선후행(Comovement) 판별
단순 상관관계를 넘어, 시차를 고려한 인과성을 검증하여 분석의 논리적 근거를 확보했습니다.
* **분석 기법**: 최대 3개월의 시차(Lag)를 설정하여 p-value < 0.05인 유의미한 품목 쌍 추출.
* **인사이트**: 특정 원자재 수입량 변동이 평균 2개월 뒤 가공품 수입량에 반영됨을 수치화함.

![Granger Causality Heatmap](https://github.com/[사용자아이디]/[레포이름]/raw/main/images/heatmap.png)  
*<p align="left">그림 1. 품목 간 Granger Causality Test 결과 (p-value Heatmap)</p>*

### ✅ 결과 2. Gradient Boosting 기반 정밀 예측
결측치 처리에 강점이 있고 비선형 패턴 학습에 유리한 **HistGradientBoostingRegressor**를 활용했습니다.
* **특징**: 단일 고성능 부스팅 모델을 최적화하여 연산 효율성과 예측 성능을 동시에 확보.
* **핵심 변수**: 선행 품목의 1~3개월 전 시차 변수(Lagged Features)를 포함하여 예측 성능 극대화.

### ✅ 결과 3. 예측 타당성 검증
2025년 8월 예측치와 과거 시계열 추세를 비교하여 모델이 실제 무역 흐름을 잘 반영하는지 확인했습니다.

![Actual vs Predicted](https://github.com/[사용자아이디]/[레포이름]/raw/main/images/prediction_result.png)  
*<p align="left">그림 2. 주요 후행 품목의 실제 추세 및 2025.08 예측 결과 시각화</p>*

-----------------------------------------------------

## 5. 기대 효과 및 활용 방안
* **공급망 관리**: 수입량 급감/급증에 대한 선행 지표를 확보하여 리스크 사전 대응 가능.
* **재고 최적화**: 후행 품목의 수요 예측 정확도를 높여 물류 비용 절감에 기여.

