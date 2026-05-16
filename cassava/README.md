# Cassava Leaf Disease Classification

Kaggle 대회 Cassava Leaf Disease Classification 실험 기록입니다.
카사바 잎 이미지로 병해 5종을 분류하는 멀티클래스 분류 태스크입니다.

## Task

- **유형**: Image Classification
- **클래스**: 5종 (CBB, CBSD, CGM, CMD, Healthy)
- **평가 지표**: Accuracy
- **데이터**: train 17,117장 / test 4,280장 (8:2 stratified split)

## 실험 결과

| 실험 | Val Accuracy |
|------|-------------|
| Baseline (ResNet18 + torchvision) | 80.77% |
| + Albumentations Augmentation | 84.65% |
| + CosineAnnealingLR Scheduler | **85.30%** |

## 주요 실험 내용

### 모델
- ResNet18 → ResNet34, EfficientNet-B4로 백본 업그레이드 실험
- 출력층 수정: `nn.Linear(n_features, 5)`
- Dropout 추가로 과적합 방지

### 데이터 증강
`torchvision` → `albumentations` 전환 (더 빠른 속도, 풍부한 기법)

| 기법 | 역할 |
|------|------|
| `RandomResizedCrop` | 다양한 스케일 학습 |
| `HorizontalFlip` | 좌우 반전 |
| `VerticalFlip` | 상하 반전 |
| `ShiftScaleRotate` | 이동/스케일/회전 |
| `HueSaturationValue` | 색상·채도·명도 변화 |
| `RandomErasing` | 이미지 일부 가림, 일반화 향상 |
| `Normalize (ImageNet)` | 사전학습 모델 정규화 |

### 학습 최적화

```python
criterion = nn.CrossEntropyLoss()
optimizer = torch.optim.AdamW(model.parameters(), lr=1e-4, weight_decay=1e-6)
scheduler = torch.optim.lr_scheduler.CosineAnnealingLR(optimizer, T_max=20, eta_min=1e-6)
```

- `WeightedRandomSampler` — 클래스 불균형 해소
- `AMP (GradScaler)` — 혼합 정밀도 학습으로 속도 향상
- `EarlyStopping` — 과적합 방지
- `TTA`, `K-Fold` — 앙상블 실험 (검증 점수 과대평가 가능성 확인)

## 주요 학습 포인트

- 데이터 증강 후 loss 증가는 오버피팅 억제의 신호 — val accuracy 기준으로 판단해야 함
- K-Fold + 앙상블 적용 시 val score가 실제 성능보다 과대평가될 수 있음
- albumentations는 torchvision 대비 다양한 기법과 빠른 처리 속도 제공
- CosineAnnealingLR은 초반 빠른 학습 + 후반 안정적 수렴에 유리

## 상세 실험 기록

[Notion 회의록](https://swift-cartwheel-7ba.notion.site/3512e7ba1f178002ad5bd121f9a709b0?v=3512e7ba1f17800c8db3000cc53dde82&pvs=74)
