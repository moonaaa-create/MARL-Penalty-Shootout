# ⚽ Multi-Agent Reinforcement Learning for 3D Soccer Penalty Shootout

> **Unity 3D 및 ML-Agents 기반의 경쟁적 멀티 에이전트 강화학습(Competitive MARL) 프로젝트**
> 
> Agent K(Kicker)와 Agent G(Goalkeeper)가 제로섬 게임(Zero-Sum Game) 구도에서 상호작용하며 최적의 슛 및 방어 전략을 학습하도록 설계된 프로젝트입니다.

---

## 📌 Project Overview (프로젝트 개요)

본 프로젝트는 Unity 3D 물리 엔진을 활용하여 페널티킥 상황을 정교하게 시뮬레이션하고, Python 연동 강화학습을 통해 두 에이전트 간의 경쟁적 학습(Adversarial Learning)을 구현했습니다.

* **Agent K (Kicker):** 공의 격타 지점(Target Point)과 임팩트 파워(Power)를 연속적 행동 공간(Continuous Action Space)으로 제어하여 득점 성공률을 극대화하도록 학습합니다.
* **Agent G (Goalkeeper):** 공의 3D 궤적 및 속도를 관측하여 최적의 다이빙 방향과 타이밍을 연속적/이산적 행동으로 제어하여 골을 방어하도록 학습합니다.

---

## 🎬 Demo Video & Downloads (시연 영상 및 자료)

![Penalty Shootout Demo Preview](https://via.placeholder.com/800x450.png?text=Agent+K+vs+Agent+G+Demo+Preview)

* 🎥 **[Full Demo & Trial-and-Error Videos (Google Drive)](https://drive.google.com/drive/folders/1DzslFsql4cC6NcKh-MyT0FbeR1C3miFd?usp=drive_link)**
* 📦 **[Pre-trained Models & Training Logs (Google Drive)](https://drive.google.com/drive/folders/1DzslFsql4cC6NcKh-MyT0FbeR1C3miFd?usp=drive_link)**

---

## 🧠 Agent & Environment Design (에이전트 및 환경 설계)

### 1. State & Action Space

| 구분 | Agent K (Kicker) | Agent G (Goalkeeper) |
| :--- | :--- | :--- |
| **Observation Space** | • 골대 기준 상대 위치<br>• Agent G의 위치 및 자세<br>• 공의 위치 | • 공의 3D 위치 및 속도 Vector<br>• Agent K의 시선/자세 및 거리<br>• 골대 중앙 기준 좌표 |
| **Action Space** | **Continuous (연속형)**<br>• 공 임팩트 지점 $(x, y)$<br>• 타격 파워 (Power) | **Continuous / Discrete (혼합형)**<br>• 다이빙 방향 (좌/우/상/하)<br>• 점프 및 이동 타이밍 |

### 2. Reward Structure & Acceptance Level (응락수준)

두 에이전트는 서로 반대되는 보상을 얻는 Zero-Sum 구조를 가집니다.

* **Agent K:** 득점 성공 시 `+1.0`, 실축/방어 당함/유효 범위를 벗어난 슛 시 `-1.0`
* **Agent G:** 골 방어(선방) 성공 시 `+1.0`, 득점 허용 시 `-1.0`
* **Acceptance Level (응락수준 기준):**
  * Agent K와 Agent G 모두 초기 무작위 행동(Random Walk) 대비 **승률 50% 이상 수렴 및 고도화된 전략(구석 슛, 예측 다이빙 등) 출현**을 성공 기준(Acceptance Level)으로 정의합니다.

---

## 🛠️ Trials & Errors (시행착오 및 해결 과정)

프로젝트 진행 과정에서 알고리즘 자체보다 **안정적인 환경 세팅(Environment Setup)** 구축이 학습 성패를 좌우한다는 점을 검증했습니다.

### 1. 환경 세팅 및 물리 엔진 동기화 (Environment & Physics Synchronization)
* **문제:** 3D 공의 회전과 반발력 등 물리 엔진 연산 속도와 강화학습 Decision Frequency 간 동기화가 어긋나 학습 초기 에피소드 폭발 발생.
* **시행착오 수치:**
  * 기본 Physics Step 사용 시: 에이전트 수렴 실패 (Loss 발산 및 무작위 다이빙 지속)
  * Decision Frequency $1 \to 5$ 조절 및 Fixed Timestep ($0.02 \to 0.01$) 재설정 후: 학습 안정화 달성

### 2. 보상 함수 편향 극복 (Reward Shaping & Policy Bias)
* **문제:** 초기 단순 보상 설계 시 Agent K는 무조건 최대 파워 중앙 슛만 시도하고, Agent G는 한쪽 방향으로만 다이빙하는 편향(Exploitation Failure) 현상 발생.
* **해결:** 유효 궤적 가산 보상과 반응 속도 Penalty/Reward를 가미하여 에피소드 약 50,000회 이후 양 에이전트 간 균형 있는 학습 달성.

---

## 🛠️ Tech Stack (기술 스택)

* **Environment & Engine:** Unity 3D (2022.3 LTS+), Unity ML-Agents Toolkit
* **RL Framework & Algorithms:** Python, PyTorch, PPO (Proximal Policy Optimization)
* **Analysis & Visualization:** TensorBoard, Matplotlib

---

## 🚀 Getting Started (시작 가이드)

### Prerequisites
* Python 3.8+
* Unity 2022.3 LTS 이상
* PyTorch & `mlagents` 패키지

### Setup & Run Commands

```bash
# 1. Repository Clone
git clone [https://github.com/](https://github.com/)[Your-Username]/[Your-Repo-Name].git
cd [Your-Repo-Name]

# 2. Virtual Environment Setup
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install Dependencies
pip install -r requirements.txt

# 4. ML-Agents Training
mlagents-learn config/trainer_config.yaml --run-id=Penalty_MARL_v1

# 5. Evaluation / Inference
python evaluate.py --model-path ./saved_models/
