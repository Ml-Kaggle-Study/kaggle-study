# Global Wheat Detection — YOLOv8 Baseline

## Dataset

- train 2,698 / val 675 (8:2 split), 이미지 크기 1024×1024
- 이미지당 평균 bbox 43.82개 (최소 1 / 최대 116)
- 출처 7개 기관, ethz_1·arvalis_1이 대부분을 차지해 출처별 불균형 존재

## 전처리

YOLO 입력 형식에 맞게 픽셀 좌표를 0~1 상대값으로 변환.
캐글 환경 특성상 원본 이미지를 symlink로 연결하고 label txt를 별도 생성했다.

## 모델

- YOLOv8n (pretrained), epochs=10, imgsz=1024, batch=8
- optimizer: AdamW (auto 선택), AMP 자동 적용

## 결과

| 지표 | 값 |
|------|-----|
| mAP50 | 0.9421 |
| mAP50-95 | 0.5415 |
| Precision | 0.9142 |
| Recall | 0.8828 |
