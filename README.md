# ♻️ 2024 Embedded AI Project – 재활용품 분류 모델 경량화

본 프로젝트는 **플라스틱/캔 이진 분류 모델**을 임베디드 환경(Jetson nano)에서 실시간으로 구동할 수 있도록 **Pruning**과 **Knowledge Distillation** 기법을 적용해 경량화한 프로젝트입니다. 2024년 임베디드 AI 과목 팀/개인 프로젝트 결과물을 정리했습니다.

---

## 프로젝트 동기

분리수거 시 올바른 종류로 분류하기 어려워 발생하는 환경 오염 문제를 해결하고자, 시험 기간에 많이 소비되는 에너지드링크 캔 테이크아웃 커피 컵(플라스틱)을 대상으로 자동 분류 모델을 개발했습니다.

## 데이터셋

| 클래스       | 샘플 수      |
| --------- | --------- |
| Can       | 1,391     |
| Plastic   | 1,391     |
| **Total** | **2,782** |

> 상세 데이터 예시는 `docs/dataset_sample/` 참조

## 모델 및 경량화 기법

### 1. Baseline

* Custom CNN 기반 (PyTorch)

### 2. Pruning

* `torch-pruning` 의 Dependency Graph로 Conv2d, Linear layer 채널 단위 구조적 Pruning
* Ratio: **0.0 / 0.25 / 0.50 / 0.75**

### 3. Knowledge Distillation (KD)

* Teacher : Baseline 모델
* Student : Light CNN
* Loss : `α * CE + (1−α) * KLDiv(T, S, T=4)`

## 실행 흐름

1. 카메라 ON ↔ 실시간 프레임 표시
2. 키보드 **`s`** 입력 ⇒ 3 초간 프레임 고정 후 분류 수행
3. 음성 안내 : “*(플라스틱/캔)으로 분류하세요.*”
4. Inference time 누적•평균 계산
5. **`q`** 입력 시 종료 & 평균 추론시간 출력


## 결과 요약

| 모델                         | 파라미터 ↓  | FLOPs ↓ | Accuracy     |
| -------------------------- | ------- | ------- | ------------ |
| Baseline                   | 100 %   | 100 %   | *see report* |
| Conv+Linear Pruning (50 %) | ↓ ≈60 % | ↓ ≈65 % | —            |
| KD Student                 | ↓ ≈70 % | ↓ ≈72 % | —            |


## 향후 과제

* 양자화(Quantization) 적용 및 성능‑용량 Trade‑off 분석
* 분류 대상 · 데이터셋 범위 확대 (유리, 종이 등 다중 클래스)
