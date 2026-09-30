# 주행 제어

계획된 경로를 따라 로봇을 움직이는 방법을 정리한 문서입니다. Local Planner, LiDAR 기반 반응형 장애물 회피, Look-ahead 경로 추종을 다룹니다. 원본은 강의 자료 슬라이드 106~126입니다.

행동(Action) 단계의 작업은 다음 4가지입니다(슬라이드 107).

- 계획된 경로에 따른 이동 명령 생성
- 로봇의 속도와 방향 제어
- 주행 중 위치와 자세의 실시간 보정
- 상황 변화에 따른 로봇 동작 제어

Global Planner, Local Planner, Costmap 용어는 [경로 계획](planning.md)에 있습니다.

## Local Planner

Local Planner(Controller)의 역할은 다음과 같습니다(슬라이드 109).

- Local Costmap으로 실시간 장애물 회피 경로를 계획함
- Global Path를 따라가도록 모터를 제어함
- 실시간성을 위해 약 20Hz의 빠른 주기로 계산함
- 대표 알고리즘은 DWA, TEB, MPPI임

| 알고리즘 | 전체 이름 |
|---|---|
| DWA | Dynamic Window Approach |
| TEB | Timed Elastic Band |
| MPPI | Model Predictive Path Integral |

알고리즘 전체 이름은 원본 슬라이드에 없는 표준 명칭입니다.

## 반응형 장애물 회피

반응형 제어(Reactive Control)는 지도나 경로 없이 현재 LiDAR 거리값만으로 좌우 바퀴 속도를 결정합니다(슬라이드 110~112). 입력 변수는 다음과 같습니다.

| 변수 | 의미 |
|---|---|
| `d_F`, `d_L`, `d_R` | 전방, 좌측, 우측 장애물 거리 |
| `battery` | 배터리 잔량 |
| `v_L`, `v_R` | 좌우 바퀴 속도 |
| `BASE_SPEED` | 기본 주행 속도 |
| `DISTANCE_THRESHOLD` | 장애물 판단 거리 |

### Algorithm 1: 단순 회피

전방이 막히면 더 넓은 쪽으로 제자리 회전합니다. 회전 각도는 제어하지 않습니다.

```python
while battery > BATTERY_THRESHOLD:
    if d_F > DISTANCE_THRESHOLD:
        v_L, v_R = BASE_SPEED, BASE_SPEED        # 직진
    elif d_L > d_R:
        v_L, v_R = -BASE_SPEED, BASE_SPEED       # 좌회전
    else:
        v_L, v_R = BASE_SPEED, -BASE_SPEED       # 우회전
    set_velocity(v_L, v_R)
```

### Algorithm 2: Yaw 기반 회피

`MOVE`, `TURN` 2개 상태를 사용합니다. 전방이 막히면 목표 yaw를 정하고 목표 각도에 도달할 때까지 회전합니다.

```python
while battery > BATTERY_THRESHOLD:
    current_yaw = get_yaw()
    if state == "MOVE":
        if d_F > DISTANCE_THRESHOLD:
            v_L, v_R = BASE_SPEED, BASE_SPEED
        else:
            state = "TURN"
            turn_dir = 1 if d_L > d_R else -1
            target_yaw = current_yaw + turn_dir * ANGLE
    elif state == "TURN":
        if abs(target_yaw - current_yaw) > TOLERANCE:
            v_L, v_R = -turn_dir * BASE_SPEED, turn_dir * BASE_SPEED
        else:
            state = "MOVE"
    set_velocity(v_L, v_R)
```

yaw 차이를 계산할 때는 $[-\pi, \pi)$ 범위로 정규화하십시오. 정규화하지 않으면 ±180° 경계에서 차이가 360° 가까이 계산됩니다.

### Algorithm 3: 코너 탈출 추가

Algorithm 2에 연속 회전 횟수 `turn_cnt`를 추가합니다. 코너에서 회전만 반복하는 상태를 검출하기 위한 값입니다.

- `TURN`으로 전환할 때마다 `turn_cnt`를 1 증가함(15행)
- 직진하면 `turn_cnt`를 0으로 초기화함(12행)
- 목표 yaw에 도달해도 `turn_cnt`를 0으로 초기화함(29행)
- `turn_cnt`가 `MAX_CNT`를 초과하면 `END_CONDITION`까지 `EscapeBehavior()`를 반복함(4~7행)

원본대로 29행에서 초기화하면 `turn_cnt`는 1을 초과하지 않습니다. 연속 회전 횟수를 누적하려면 29행의 초기화를 제거하고 직진할 때(12행)만 초기화하십시오.

## Look-ahead 경로 추종

Look-ahead 방식은 경로 위에서 일정 거리 앞의 목표점(Look-ahead Point)을 따라가도록 곡률을 계산합니다. Global Planner의 경로는 이산(Discrete) waypoint 목록입니다. 로봇의 실제 주행 궤적은 연속(Continuous) 곡선입니다(슬라이드 116).

### 처리 순서

| 단계 | 처리 내용 | 슬라이드 |
|---|---|---|
| 1. 로봇 위치 파악 | 현재 로봇 좌표에서 Euclidean Distance가 가장 가까운 waypoint 계산 | 117 |
| 2. Look-ahead Point 설정 | 가장 가까운 waypoint부터 경로 거리(직선 거리 아님) 기준으로 목표점 설정 | 118 |
| 3. 로봇 Frame 변환 | 로봇 좌표를 원점으로 이동하고 heading이 0°(+x축)가 되도록 회전 | 119~121 |
| 4. 곡률 계산 | 로봇 Frame 좌표로 곡률 $\kappa$ 계산 | 122 |
| 5. 바퀴 속도 계산 | 선속도·각속도 계산 후 좌우 바퀴 속도로 변환 | 123~125 |

1단계의 최근접 점 탐색은 `scipy.spatial.cKDTree` 또는 `sklearn.neighbors.NearestNeighbors`로 구현할 수 있습니다.

### 계산식

로봇 위치 $(x_r, y_r)$, heading $\theta$, Look-ahead Point $(x_p, y_p)$일 때 계산식은 다음과 같습니다.

**3. 로봇 Frame 변환**

$$
\Delta x = x_p - x_r, \qquad \Delta y = y_p - y_r
$$

$$
\begin{bmatrix} x_{\text{LA}} \\ y_{\text{LA}} \end{bmatrix}
=
\begin{bmatrix} \cos\theta & \sin\theta \\ -\sin\theta & \cos\theta \end{bmatrix}
\begin{bmatrix} \Delta x \\ \Delta y \end{bmatrix}
$$

$x_{\text{LA}}$는 로봇과의 앞뒤 거리, $y_{\text{LA}}$는 로봇과의 좌우 거리입니다.

**4. 곡률 (단위 $\text{m}^{-1}$)**

$$
\kappa = \frac{2\, y_{\text{LA}}}{x_{\text{LA}}^2 + y_{\text{LA}}^2}
$$

$\kappa > 0$이면 좌회전, $\kappa < 0$이면 우회전, $\kappa = 0$이면 직진입니다.

**5. 선속도·각속도와 바퀴 속도**

$$
\omega = v\,\kappa, \qquad
v_r = v + \frac{\omega L}{2}, \qquad
v_l = v - \frac{\omega L}{2}
$$

5단계의 역변환은 다음과 같습니다.

$$
v = \frac{v_r + v_l}{2}, \qquad \omega = \frac{v_r - v_l}{L}
$$

이 식은 [Wheel Odometry](slam.md#wheel-odometry)의 $\Delta s$, $\Delta\theta$ 식과 같은 형태입니다. 3단계의 회전 행렬은 슬라이드 119~121의 도식을 식으로 옮긴 것입니다.

Webots 모터의 `setVelocity()`는 바퀴 각속도(rad/s)를 입력받습니다. 바퀴 선속도를 바퀴 반지름 $R$로 나눈 값을 전달하십시오.

```python
left_motor.setVelocity(v_l / R)
right_motor.setVelocity(v_r / R)
```

### Look-ahead Distance 조정

| 설정 | 현상 |
|---|---|
| 너무 긴 거리 | 경로에서 크게 벗어날 가능성 증가, 급커브에서 경로 안쪽을 가로지름 |
| 너무 짧은 거리 | 커브에서 불안정한 주행, 지그재그 움직임 발생 |

원본은 슬라이드 126입니다.
