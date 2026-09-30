# AMR Search and Rescue 과정 안내

2026 부산대학교 TECH WEEK Physical AI 과정의 주제, 학습 범위, 해커톤 조건, 개발 환경을 정리한 문서입니다. 원본은 아래 과정 안내 이미지입니다.

![AMR Search and Rescue 과정 안내 이미지](../img/course-overview.png)

## 과정 개요

| 항목 | 내용 |
|---|---|
| 과정명 | Autonomous Mobile Robot(AMR)의 Search and Rescue |
| 문제 정의 | 사전 정보가 없는 환경을 로봇이 탐색하고 지정된 대상·위치를 찾아 안전하게 이동하는 문제 |
| 학습 파이프라인 | Perception → Localization → Mapping → Planning → Control |

Search and Rescue 기술의 적용 분야는 다음 4가지입니다.

- 재난·위험 환경의 탐색과 구조
- Warehouse 물품 탐색과 Pick and Deliver
- 도심 환경에서 사람·장애물을 회피하는 Delivery Robot
- 실내 환경에서 특정 사람·물체를 탐색하는 Service Robot

## 학습 내용

| 주제 | 학습 항목 | 상세 문서 |
|---|---|---|
| Autonomous Mobile Robot | AMR 기본 구조, 센서·인지·판단·이동 과정, 미지 환경 탐색, 산업·서비스 활용 사례 | [강의 자료 개요](../explanation/lecture/README.md), [센서](../explanation/lecture/sensors.md) |
| SLAM | 지도 생성과 자기 위치 추정 동시 수행, Odometry 이동량 추정, LiDAR 환경 인식, 센서 기반 위치 보정 | [SLAM](../explanation/lecture/slam.md) |
| Object Detection | 카메라 환경 인식, 목표 물체 탐지, 규칙 기반 Computer Vision, 학습된 Deep Learning 모델 | [Computer Vision](../explanation/lecture/computer-vision.md) |
| Path Planning | 목적지 경로 계산, 장애물 고려 경로 탐색, Global·Local Path Planning, 실시간 장애물 회피, Dynamic Obstacle 대응 | [경로 계획](../explanation/lecture/planning.md), [주행 제어](../explanation/lecture/control.md) |

## 해커톤 주제

해커톤 주제는 Autonomous Search and Rescue입니다. 로봇은 미지의 환경을 탐색하고 지정된 대상을 찾아야 합니다. 대상을 찾은 뒤에는 목적지까지 이동하고 시작 지점으로 복귀해야 합니다. 이동 중에는 장애물과 움직이는 사람에 충돌하면 안 됩니다.

## 해커톤 조건

| 항목 | 조건 |
|---|---|
| 지도 | 사전 제공 없음 |
| 로봇 현재 위치 | 알 수 없음 |
| 시작 지점 | position·orientation 제공 |
| 목표 물체 위치 | 알 수 없음 |
| 목표 물체 정보 | 종류와 시각적 특징 제공 |
| 충돌 | 정적 장애물·이동하는 사람과 충돌 금지 |

## 활용 가능한 핵심 기술

| 기술 | 내용 |
|---|---|
| Mapping | 시작 지점부터 LiDAR·Odometry 데이터로 지도 생성, NumPy 기반 Occupancy Grid Map 작성 |
| Localization | 이동 중 현재 위치·방향 추정, Scan Matching 활용 |
| Object Detection | 규칙 기반 Computer Vision, 학습된 딥러닝 모델 |
| Global Path Planning | 작성한 지도로 현재 위치에서 목표 위치까지 전체 경로 계산, Dijkstra·A\* |
| Local Path Planning | 로봇 주변 실시간 센서 데이터로 근거리 장애물 회피와 이동 방향 결정, Dynamic Window Approach 등 Critic 기반 Local Planner |

## 개발 환경

| 항목 | 내용 |
|---|---|
| 시뮬레이터 | Webots |
| 기준 환경 | Ubuntu 22.04, Python 3.10 |
| 사용 가능 OS | Windows, macOS |
| 사용 가능 언어 | Python, C++ |
| GPU | 선택 사항 |

## 목표 물체 모델

![Webots Apple PROTO 아이콘](../img/apple-icon.png)

`protos/`에는 목표 물체용 사과 PROTO 4종이 있습니다. 4종 모두 Webots 기본 Apple 모델을 기준으로 작성했습니다. 위 아이콘은 `protos/icons/Apple.png`입니다.

| PROTO | 이름 | 표면 |
|---|---|---|
| `RedApple` | Red Apple | 단색 `baseColor 1 0 0` |
| `GreenApple` | Green Apple | Webots 기본 사과 이미지 텍스처 |
| `OrangeApple` | Orange Apple | 단색 `baseColor 1 0.73 0` |
| `PurpleApple` | Purple Apple | 단색 `baseColor 0.56 0 1` |

공통 값은 크기 0.05 × 0.05 × 0.05 m, 질량 0.15 kg입니다. `worlds/breakroom_teleop_yolo.wbt`는 4종을 모두 배치합니다. `worlds/apartment.wbt`는 `RedApple`을 사용합니다.

사과는 COCO 데이터셋의 47번 클래스(`apple`)입니다. 학습된 YOLO 모델로 탐지할 때의 클래스 번호는 [COCO 클래스 목록](coco-classes.md)에 있습니다.
