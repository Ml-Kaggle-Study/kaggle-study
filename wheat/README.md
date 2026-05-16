# Global Wheat Detection

Kaggle 대회 Global Wheat Detection 실험 기록입니다.
밀 이삭(wheat head)을 이미지에서 탐지하는 Object Detection 태스크입니다.

## Task

- **유형**: Object Detection
- **클래스**: wheat (단일 클래스)
- **평가 지표**: mAP (IoU 기준)
- **데이터**: train 2,698장 / val 675장 (1024×1024, 8:2 split)
- **특이사항**: train(유럽·북미) / test(호주·일본·중국) 도메인 차이 존재

## 실험 결과

| 실험 | mAP50 | mAP50-95 |
|------|-------|----------|
| Baseline (YOLOv8n, epoch=10) | 0.9421 | 0.5415 |
| + Augmentation + YOLOv8m + epoch=20 | - | 미세 변화 |

## 주요 실험 내용

### 모델
- YOLOv8 계열: `yolov8n` → `yolov8s` → `yolov8m`
- Faster R-CNN (`fasterrcnn_resnet50_fpn`) 비교 실험

### 전처리
bbox `[x, y, w, h]` 픽셀 좌표 → YOLO 형식(0~1 상대값)으로 변환:

```python
dw, dh = 1./size[0], 1./size[1]
x_center = box[0] + box[2] / 2.0
y_center = box[1] + box[3] / 2.0
return (x_center * dw, y_center * dh, box[2] * dw, box[3] * dh)
```

### 데이터 증강

| 기법 | 내용 |
|------|------|
| Mosaic | 4개 이미지 합성, 다양성 확보 |
| Mixup | 이미지 반투명 혼합, 과적합 감소 |
| RandomFlip (lr/ud) | 방향 불변성 확보 |
| Scale | 랜덤 확대/축소 |
| HSV 변환 | 색상·채도·밝기 변화 |
| Multi-scale Training | 학습 중 이미지 크기 랜덤 변경 |

### 학습 최적화

| 기법 | 내용 |
|------|------|
| CosineAnnealingLR | lr 안정적 감소 |
| AdamW | SGD 대비 안정적 수렴 |
| Freeze (`freeze=10`) | 초기 레이어 고정, head 중심 학습 |
| EarlyStopping (`patience`) | 과적합 방지 |
| TTA | 예측 안정성 목적 (성능 하락 확인) |
| Label Smoothing | 과신 방지 |
| Confidence Threshold | 0.25 → 0.001 (작은 객체 대응) |

## 주요 학습 포인트

- mAP50-95는 box 위치 정밀도까지 요구 — 단순 증강·파라미터 튜닝만으로는 한계
- 작은 객체 탐지에서 imgsz를 낮추면 성능에 직접적 타격
- TTA가 오히려 성능을 낮추는 경우도 있음 — 데이터 특성에 따라 다름
- Faster R-CNN은 YOLO 대비 구현 복잡도가 높으나 다른 접근 방식 이해에 유효

## 상세 실험 기록

[Notion 회의록](https://swift-cartwheel-7ba.notion.site/3512e7ba1f178002ad5bd121f9a709b0?v=3512e7ba1f17800c8db3000cc53dde82&pvs=74)
