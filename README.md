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
| ML | Scikit-learn |
| Visualization | Matplotlib, Seaborn |
| Environment | Google Colab (GPU T4) |

-----------------------------------------------------

## 2. 분석 프로세스 (Workflow)

1.  **데이터 변환 (Data Format Conversion)**
    * csv를 Parquet로 변환하아ㅕ 대용량 데이터의 빠른 입출력을 위한 포맷 변환 
    * 시계열 연속성 확보를 위한 선형 보간(Interpolation) 수행.

2.  **Feature Engineering**
    * 출발/도착 시간, 항공사, 공항 등 범주형 변수 식별.
    * 결측치 플래그(Missing Indicator) 생성 및 불필요한 컬럼 제거.

3.  **Model Training (Catboost)**
    * 레이블이 존재하는 데이터셋 추출 후 `CatBoostClassifier` 학습.
    * GPU 모드 및 범주형 피처 자동 처리 설정.

4.  **Inference & Submission**
    * 테스트 데이터셋에 대한 지연 확률 도출 및 제출 파일 생성.

-----------------------------------------------------

## 3. 🚀 Key Results (핵심 성과)
### ✅ 결과 1. 고효율 데이터 파이프라인 구축 (Memory Optimization)
대규모 CSV 파일을 그대로 로딩하는 비효율성을 해결하기 위해 데이터 저장 형식을 최적화했습니다.
* **CSV to Parquet**: 초기 1회 변환을 통해 로딩 속도를 개선하고 메모리 점유율을 대폭 낮춤.
* **Data Type Casting**: 불필요한 데이터 타입을 정리하여 분석 환경의 안정성 확보.

### ✅ 결과 2. 강력한 결측치 처리 전략 (Missing Indicator)
데이터 내 다수 존재하는 결측치를 단순 삭제하지 않고 모델이 학습할 수 있는 정보로 변환했습니다.
* **Missing Indicator**: 결측 여부 자체를 새로운 피처로 추가하여, 데이터의 누락 패턴이 지연에 미치는 영향력을 모델이 학습하도록 유도.
* **CatBoost Native Handling**: 범주형 변수의 결측치를 CatBoost 내부 알고리즘이 효과적으로 처리하도록 설정.

### ✅ 결과 3. CatBoost 분류 모델 최적화
범주형 변수가 많은 항공 데이터의 특성에 맞춰 최적의 알고리즘을 선정했습니다.
* **GPU 가속**: Colab T4 GPU를 활용하여 학습 속도 단축.
* **Probability Prediction**: 단순 분류가 아닌 `predict_proba`를 활용하여 지연 가능성을 확률값으로 산출, 예측의 정밀도 향상.

-----------------------------------------------------
## 4. 🧠 Troubleshooting (트러블슈팅)

### ❓ Issue 1: CatBoost `cat_features`의 데이터 타입 충돌
* **문제**: 범주형 변수로 지정한 컬럼에 결측치(`NaN`)가 포함되어 있어, Pandas가 해당 컬럼의 dtype을 자동으로 `float`로 설정함.
            <br>CatBoost는 범주형 변수로 **문자열(String) 또는 정수(Integer)만 허용**하므로 `float` 타입 포함 시 에러 발생.
* **원인**: `20253.0`과 같이 소수점이 포함된 실수형 데이터는 CatBoost의 범주형 피처로 직접 사용할 수 없음.
* **해결**: 
    1. 지정한 범주형 컬럼 전체를 `.astype(str)`을 통해 **문자열 타입으로 강제 형변환**.
    2. 소수점이 포함된 형태(예: '20253.0')까지 모두 문자열로 통일하여 모델이 범주형 데이터로 인식할 수 있도록 처리함.
 
### ❓ Issue 2: 레이블 누락 및 결측 데이터 처리
* **문제**: 타겟 레이블이 없는 데이터와 피처 내 결측치가 혼재되어 학습이 어려움.
* **해결**: 결측치를 단순히 평균이나 최빈값으로 채우는 대신, **결측 여부(Indicator)**를 피처로 활용하여 모델이 데이터의 부재 패턴을 스스로 파악하게 함으로써 예측력을 높임.

---

## 5. 📝 회고 (Retrospective)

* **학습 내용**: CatBoost를 사용할 때 데이터 타입(dtype)에 따른 엄격한 제약 조건을 경험하며, 전처리 단계에서의 데이터 타입 관리의 중요성을 깨달았습니다.
* **성공적인 점**: 단순한 평균치 대체를 넘어 '결측 여부'를 정보로 활용하는 전략을 통해 데이터 손실 없이 예측 성능을 방어할 수 있었습니다.
* **향후 과제**: 레이블이 없는 대량의 데이터를 활용하기 위해 **Pseudo-labeling**이나 **준지도 학습(Semi-supervised Learning)** 기법을 추가로 연구해보고 싶습니다.
---

## 6. 기대 효과 및 활용 방안
* **데이터 효율화**: 대규모 정형 데이터를 처리하는 표준화된 파이프라인(Parquet + CatBoost) 제안.
* **예측 정확도**: 항공사 및 공항별 지연 패턴을 확률 기반으로 분석하여 운영 효율성 제고 자료로 활용 가능.

