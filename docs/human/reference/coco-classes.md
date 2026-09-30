# COCO 클래스 목록

COCO(Common Objects in Context) 데이터셋의 80개 클래스와 ID를 정리한 문서입니다. 원본은 강의 자료 슬라이드 49~50입니다. COCO로 학습한 YOLO 모델은 이 ID를 클래스 번호로 사용합니다.

## 데이터셋 정보

| 항목 | 내용 |
|---|---|
| 이미지 내용 | 일상 장면 속 다양한 객체의 컬러 이미지 |
| 이미지 크기 | 이미지마다 다름 |
| 채널 | RGB 3채널 |
| 클래스 | 80개 |
| 전체 이미지 | 약 330,000장 |
| 객체 인스턴스 | 약 1,500,000개 |
| 주요 용도 | 객체 탐지, 분할, 키포인트, 캡셔닝 |

## 해커톤 관련 클래스

| ID | Class | 사용 위치 |
|---|---|---|
| 32 | `sports ball` | `controllers/tb3_teleop_yolo/tb3_teleop_yolo.py` 주석의 필터 예시 |
| 47 | `apple` | 목표 물체 사과 PROTO 4종, `tb3_teleop_yolo.py` 주석의 필터 예시 |
| 49 | `orange` | `tb3_teleop_yolo.py` 주석의 필터 예시 |

`tb3_teleop_yolo.py`는 `classes=None`으로 전체 클래스를 탐지합니다. 특정 클래스만 탐지하려면 `classes=[47]`처럼 ID 목록을 지정하십시오.

## 전체 클래스

| ID | Class | 의미 |
|---|---|---|
| 0 | `person` | 사람 |
| 1 | `bicycle` | 자전거 |
| 2 | `car` | 자동차 |
| 3 | `motorcycle` | 오토바이 |
| 4 | `airplane` | 비행기 |
| 5 | `bus` | 버스 |
| 6 | `train` | 기차 |
| 7 | `truck` | 트럭 |
| 8 | `boat` | 보트 |
| 9 | `traffic light` | 신호등 |
| 10 | `fire hydrant` | 소화전 |
| 11 | `stop sign` | 정지 표지판 |
| 12 | `parking meter` | 주차 미터기 |
| 13 | `bench` | 벤치 |
| 14 | `bird` | 새 |
| 15 | `cat` | 고양이 |
| 16 | `dog` | 개 |
| 17 | `horse` | 말 |
| 18 | `sheep` | 양 |
| 19 | `cow` | 소 |
| 20 | `elephant` | 코끼리 |
| 21 | `bear` | 곰 |
| 22 | `zebra` | 얼룩말 |
| 23 | `giraffe` | 기린 |
| 24 | `backpack` | 배낭 |
| 25 | `umbrella` | 우산 |
| 26 | `handbag` | 핸드백 |
| 27 | `tie` | 넥타이 |
| 28 | `suitcase` | 여행 가방 |
| 29 | `frisbee` | 원반 |
| 30 | `skis` | 스키 |
| 31 | `snowboard` | 스노보드 |
| 32 | `sports ball` | 스포츠 공 |
| 33 | `kite` | 연 |
| 34 | `baseball bat` | 야구 방망이 |
| 35 | `baseball glove` | 야구 글러브 |
| 36 | `skateboard` | 스케이트보드 |
| 37 | `surfboard` | 서프보드 |
| 38 | `tennis racket` | 테니스 라켓 |
| 39 | `bottle` | 병 |
| 40 | `wine glass` | 와인잔 |
| 41 | `cup` | 컵 |
| 42 | `fork` | 포크 |
| 43 | `knife` | 나이프 |
| 44 | `spoon` | 숟가락 |
| 45 | `bowl` | 그릇 |
| 46 | `banana` | 바나나 |
| 47 | `apple` | 사과 |
| 48 | `sandwich` | 샌드위치 |
| 49 | `orange` | 오렌지 |
| 50 | `broccoli` | 브로콜리 |
| 51 | `carrot` | 당근 |
| 52 | `hot dog` | 핫도그 |
| 53 | `pizza` | 피자 |
| 54 | `donut` | 도넛 |
| 55 | `cake` | 케이크 |
| 56 | `chair` | 의자 |
| 57 | `couch` | 소파 |
| 58 | `potted plant` | 화분 |
| 59 | `bed` | 침대 |
| 60 | `dining table` | 식탁 |
| 61 | `toilet` | 변기 |
| 62 | `tv` | TV |
| 63 | `laptop` | 노트북 |
| 64 | `mouse` | 마우스 |
| 65 | `remote` | 리모컨 |
| 66 | `keyboard` | 키보드 |
| 67 | `cell phone` | 휴대전화 |
| 68 | `microwave` | 전자레인지 |
| 69 | `oven` | 오븐 |
| 70 | `toaster` | 토스터 |
| 71 | `sink` | 싱크대 |
| 72 | `refrigerator` | 냉장고 |
| 73 | `book` | 책 |
| 74 | `clock` | 시계 |
| 75 | `vase` | 꽃병 |
| 76 | `scissors` | 가위 |
| 77 | `teddy bear` | 곰 인형 |
| 78 | `hair drier` | 헤어드라이어 |
| 79 | `toothbrush` | 칫솔 |
