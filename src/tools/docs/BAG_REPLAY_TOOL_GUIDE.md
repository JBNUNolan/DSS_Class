# ROS2 Bag Replay Tool 사용 가이드

## 개요

ROS2 bag 파일을 재생하기 위한 GUI 도구입니다. ROS1에서 ROS2로 변환되었습니다.

---

## 주요 기능

| 기능 | 설명 |
|------|------|
| **파일 선택** | GUI를 통해 ROS2 bag 디렉토리 선택 |
| **최근 파일** | 최대 10개의 최근 사용 파일 접근 가능 |
| **토픽 선택** | bag 파일의 모든 토픽 확인 및 선택 |
| **재생 컨트롤** | Play, Pause, Stop 기능 |
| **재생 옵션** | 루프 재생, 재생 속도 조정(0.1x~10x), 클록 발행 |
| **Bag 정보 표시** | 선택된 bag 파일의 상세 정보 표시 |

---

## 설치 방법

```bash
cd ~/ros2_ws
colcon build --packages-select bag_replay_tool_ros2
source install/setup.bash
```

---

## 실행 방법

### 1. Launch 파일로 실행

```bash
ros2 launch bag_replay_tool_ros2 bag_replay_tool.launch.py
```

### 2. 직접 실행

```bash
ros2 run bag_replay_tool_ros2 bag_replay_tool_node
```

---

## 사용 가이드

### 1단계: Bag 파일 선택

다음 3가지 방법 중 선택:

- **Browse... 버튼**: 직접 ROS2 bag 디렉토리 선택
- **File → Open Bag File... (Ctrl+O)**: 파일 다이얼로그 열기
- **File → Recent Files**: 최근 사용 파일 목록에서 선택

> **주의**: ROS2 bags는 `metadata.yaml`과 `.db3` 파일을 포함하는 디렉토리입니다.

### 2단계: 토픽 확인 및 선택

- 좌측 패널에 모든 토픽 목록 표시
- 체크박스로 발행할 토픽 선택/해제
- 기본값: 모든 토픽 선택됨

### 3단계: 재생 옵션 설정

| 옵션 | 설명 |
|------|------|
| **Loop Playback** | 지속적인 반복 재생 활성화 |
| **Playback Rate** | 재생 속도 조정 (기본값: 1.0x) |
| **Publish Clock Time** | /clock 토픽 발행 여부 |

### 4단계: 재생 제어

| 버튼 | 기능 |
|------|------|
| **Play** | 선택된 토픽으로 재생 시작 |
| **Pause** | 일시 정지 (재개 가능) |
| **Stop** | 재생 중지 |

---

## 필수 의존성

| 패키지 | 설명 |
|--------|------|
| rclpy | ROS2 Python 클라이언트 |
| rosbag2_py | ROS2 bag 파일 처리 |
| PyQt5 | GUI 프레임워크 |
| python-yaml | YAML 파일 처리 |

### Python 의존성 설치

```bash
pip3 install PyQt5
```

---

## 패키지 구조

```
bag_replay_tool_ros2/
├── bag_replay_tool_ros2/
│   ├── __init__.py
│   └── bag_replay_tool_node.py          # 메인 파이썬 노드
├── ui/
│   └── bag_replay_tool.ui               # Qt Designer UI 파일
├── launch/
│   └── bag_replay_tool.launch.py        # ROS2 launch 파일
├── resource/
│   └── bag_replay_tool_ros2             # 패키지 마커
├── package.xml
├── setup.py
├── setup.cfg
└── README.md
```

---

## ROS1 vs ROS2 주요 차이점

### 1. Bag 파일 형식

| 구분 | ROS1 | ROS2 |
|------|------|------|
| 파일 형식 | 단일 `.bag` 파일 | `metadata.yaml` + `.db3` 파일 |
| 선택 방식 | 파일 선택 | 디렉토리 선택 |

### 2. API 변경

| ROS1 | ROS2 |
|------|------|
| `rospy` | `rclpy` |
| `rosbag` | `rosbag2_py` |
| 직접 API 사용 | `ros2 bag play` 명령어 사용 |

### 3. 재생 방식

| 항목 | ROS1 | ROS2 |
|------|------|------|
| 재생 방법 | rosbag Python API 직접 사용 | `ros2 bag play` 서브프로세스 |
| 일시 정지 | 직접 제어 | SIGSTOP/SIGCONT 신호 사용 |

### 4. 빌드 시스템

| 항목 | ROS1 | ROS2 |
|------|------|------|
| 빌드 도구 | catkin + CMakeLists.txt | ament_python + setup.py |

---

## 설정 파일 위치

- **최근 파일 목록**: `~/.ros2/bag_replay_tool_recent.json`
- **최근 파일 초기화**: File → Clear Recent Files

---

## 문제 해결

### 문제 1: UI 파일을 찾을 수 없음

```bash
colcon build --packages-select bag_replay_tool_ros2 --symlink-install
source install/setup.bash
```

### 문제 2: PyQt5를 찾을 수 없음

```bash
pip3 install PyQt5
```

### 문제 3: Invalid bag 디렉토리 오류

- ROS2 bags는 디렉토리이며, `metadata.yaml` 파일이 포함되어야 합니다.
- 파일이 아닌 **디렉토리**를 선택하세요.

---

## ROS1에서 ROS2로 마이그레이션 가이드

### 1단계: Bag 파일 변환

ROS1 bag 파일을 ROS2 형식으로 변환:

```bash
ros2 bag convert -i input.bag -o output_ros2_bag
```

### 2단계: 사용 방법 조정

| 항목 | ROS1 | ROS2 |
|------|------|------|
| 선택 대상 | `.bag` 파일 | 디렉토리 |
| Bag 정보 형식 | 기존 형식 | 변경됨 |
| 설정 저장 위치 | `~/.ros/` | `~/.ros2/` |

---

## 기술 정보

### 재생 옵션

- **Topic Selection**: `--topics` 플래그로 토픽 필터링
- **Pause/Resume**: 서브프로세스에서 SIGSTOP/SIGCONT 신호 사용
- **Cleanup**: 종료 시 모든 프로세스 자동 정리

### 실행 시 자동 처리

- 선택된 토픽만 발행
- 설정된 재생 속도 적용
- 클록 토픽 발행 여부 자동 결정

---

## 라이선스

MIT

---

**마지막 수정**: 2026-01-28
