# Week 1 — Baseline

## 한 것
- EDA: 클래스 분포 확인, 이미지 시각화
- ResNet18 베이스라인 모델 구현
- stratified split으로 train/test 분리 (8:2)

## 결과
- Test Accuracy: **80.77%**

## 환경
- epochs: 10
- optimizer: AdamW (lr=1e-4, weight_decay=1e-6)
- loss: CrossEntropyLoss
- transform: torchvision (Resize 256x256)

