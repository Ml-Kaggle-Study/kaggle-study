# Week 2 — Data Augmentation + Scheduler

## 한 것
- torchvision → albumentations으로 전환
  - RandomResizedCrop, HorizontalFlip, VerticalFlip, ShiftScaleRotate, HueSaturationValue
  - ImageNet 정규화 적용
- CosineAnnealingLR 스케줄러 추가

## 결과
| 실험 | Accuracy |
|------|----------|
| Augmentation | 84.65% |
| Augmentation + Scheduler | **85.30%** |

## 환경
- epochs: 20
- optimizer: AdamW (lr=1e-4, weight_decay=1e-6)
- loss: CrossEntropyLoss
- scheduler: CosineAnnealingLR (T_max=20, eta_min=1e-6)