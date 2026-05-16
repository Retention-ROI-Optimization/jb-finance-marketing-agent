# JB금융 디지털 마케팅 AI Agent — Decision Engine

> JB금융그룹 지정주제 3 「마케팅AI · 디지털마케팅AI Agent 서비스개발」 출품작
> 본 레포는 마케팅 Agent의 **Decision Engine** (타겟팅·예산 최적화·추천)을 담당합니다.

## Project Context

JB금융그룹의 디지털 마케팅 워크플로우에서 본 시스템은 다음 질문에 답합니다.

- **WHO**: 어떤 고객에게 마케팅을 보내야 하는가? (이탈 위험 + Uplift 반응성)
- **WHEN**: 언제 보내야 하는가? (Survival Analysis 기반 골든타임)
- **WHICH**: 어떤 상품·액션을 추천해야 하는가? (세그먼트별 전략)
- **HOW MUCH**: 예산을 어떻게 배분해야 ROI가 최대화되는가? (Budget Optimization)

### 도메인 매핑

본 레포의 시뮬레이터 변수는 의사결정 엔진의 도메인 비종속 식별자입니다. 실제 JB금융 운영 환경에서는 어댑터 레이어를 통해 다음과 같이 매핑됩니다.

| 시뮬레이터 변수 | JB금융 도메인 의미 |
|---|---|
| `visit` event | 앱·인터넷뱅킹 접속 |
| `page_view` event | 상품 상세 조회 |
| `search` event | 금리·한도 조회 |
| `add_to_cart` event | 가입 시도 (전자약정 진입) |
| `purchase` event | 상품 가입 완료 (예적금·대출·카드·펀드·외환) |
| `support_contact` event | 고객센터·챗봇·PB 문의, 해지신청 |
| `coupon_open` / `coupon_redeem` | 마케팅 알림 열람 / 우대금리·쿠폰 사용 |
| `vip_loyal` persona | WM 우수고객 (예치자산 1억+, 거래활성 상위 10%) |
| `regular_loyal` persona | 주거래 안정고객 (급여이체 + 자동이체 + 3개 이상 상품) |
| `price_sensitive` persona | 금리민감 고객 (만기마다 타행 비교, 우대금리 강반응) |
| `explorer` persona | 신규 디지털 유입 (앱 다운로드 후 1~2개 상품만 체험) |
| `churn_progressing` persona | 이탈 진행 고객 (잔액·거래 빈도 급감, 골든타임) |
| `new_signup` persona | 신규가입 90일 이내 (온보딩 구간) |
| `monetary` feature | 금융자산 잔액 (예적금+펀드+대출잔액 가중합) |
| `frequency` feature | 월평균 거래 건수 (출금·이체·결제·가입 통합) |
| `avg_order_value` | 건당 평균 거래금액 |
| `price_sensitivity` | 금리 민감도 (만기 이탈·타행 송금 이력 기반) |
| `treatment_lift` | 캠페인 처치 효과 (우대금리 발송 시 거래 상승 정도) |

상품 카테고리(`item_category`)는 다음을 다룹니다.

`deposit` (예적금) · `loan` (대출) · `card` (카드) · `fund` (펀드·투자) · `fx` (외환·송금) · `insurance` (보험)

### Uplift 4-Segment Marketing Policy

| 세그먼트 | 정의 | 마케팅 정책 |
|---|---|---|
| **Persuadable** ★ | 캠페인 받으면 거래, 안 받으면 이탈 | 최우선 타겟 — 우대금리·캐시백 발송 |
| **Sure Thing** | 캠페인 유무 무관하게 거래 | 발송 제외 (가성비 없음) |
| **Lost Cause** | 캠페인 받아도 거래 안 함 | 발송 제외, 채널·메시지 다변화 후 재평가 |
| **Sleeping Dog** ⚠ | 캠페인 받으면 오히려 이탈 (피로·거부감) | **발송 절대 금지** (역효과) |

### 규제 적합성

- **금융소비자보호법** — 마케팅 콘텐츠 발송 전 단정 표현(`반드시`·`보장` 등) 자동 차단, 상품유형별 필수 고지문 강제 삽입 (가드레일 모듈)
- **개인정보보호법·신용정보법** — 학습·추론 단계 PII 비식별화, 외부 API에 식별정보 미전송
- **금융위 AI 가이드라인 (2021)** — 모든 의사결정에 reason code 제공 (`risk_reason_codes`, `action_reason_codes`, `guardrail_codes`) — 설명가능성 + 차별 방지
- **마이데이터 표준 API** — 본인 동의 범위 내 활용 원칙 준수, 어댑터 레이어 호환 설계

### 본선 단계 확장 (예정)

- 콘텐츠 생성 Agent (Gen AI + RAG) — 채널·언어별 마케팅 콘텐츠 자동 생성
- LangGraph 기반 멀티 Agent 오케스트레이션 시각화
- 자체 sLLM 미세조정 (외부 LLM 의존성 해소)
- JB금융 내부 CRM·MyData API 실연동 PoC

# Retention ROI Project

## Project Overview

Retention ROI Project is a data-driven decision system that covers the full retention workflow: **customer churn prediction, intervention strategy optimization, personalized recommendations, and real-time operations**.  
Rather than only predicting _who_ will churn, this system estimates **_when_ churn is likely to happen**, **_which_ offer should be given to _which_ customer for maximum ROI**, and **identifies the optimal execution priority under budget constraints**.

This project supports:

- Customer behavior analysis using simulated data
- Churn modeling and survival analysis for churn timing estimation
- Uplift, CLV, and segmentation-based targeting with budget optimization
- Customer-level action recommendations with operational explainability
- Strategy validation through A/B testing and simulation fidelity checks
- Pre-deployment validation through real-time replay pipelines

In short, this project is an end-to-end **Retention Decision Intelligence Pipeline** that helps marketing and CRM teams execute retention strategies based on data rather than intuition.

## Image

<img src="assets/dash1.jpeg" width="400"/>


<img src="assets/dash2.jpeg" width="400"/>


<img src="assets/dash3.jpeg" width="400"/>

<img src="assets/dash4.jpeg" width="400"/>


## Installment

```bash
pip install -r requirements.txt
```

## Docker Implementation

```bash
docker compose up --build
```

## Simulation Implementation

```bash
# Generate synthetic customer behavior data
python src/main.py --mode simulate
```

## Feature Engineering Implementation

```bash
# Build customer-level feature store
python src/main.py --mode features
```

Outputs:

- `data/feature_store/customer_features.csv`
- `data/feature_store/customer_features_metadata.json`
- `results/feature_engineering_summary.json`

## Churn modeling

```bash
# Train churn prediction models
python src/main.py --mode train
```

Outputs:

- `models/churn_model_<best_model>.joblib`
- `results/churn_auc_roc.png`
- `results/churn_precision_recall_tradeoff.png`
- `results/churn_shap_summary.png`
- `results/churn_shap_local.png`
- `results/churn_threshold_analysis.json`
- `results/churn_top10_feature_importance.json`
- `results/churn_metrics.json`

## Survival Ananlysis

```bash
# Estimate churn timing (time-to-event)
python src/main.py --mode survival
```

Runs the survival analysis pipeline to estimate **_when_ each customer is likely to churn**, not just whether they will churn.

This step produces **time-aware churn signals** such as expected churn timing, hazard-related outputs, and intervention windows that can later be integrated into the _optimization_ and _recommendation_ stages.

## Uplift + CLV / Segmentation / Optimization

```bash
python src/main.py --mode uplift
python src/main.py --mode clv
python src/main.py --mode segment
python src/main.py --mode optimize --budget 50000000
```

_you're allowed to write whatever budget you have in your mind._

## Personalization / Recommendation

```bash
python src/main.py --mode recommend --budget 5000000 --threshold 0.5 --max-customers 1000
```

_you can change the figures (threshod, max-customers)_

### 🔍 Operational explainability

```bash
# Generate human-readable explanations for decisions
python src/main.py --mode explain
```

Creates **operational explanation artifacts** for selected or high-priority customers.
This stage summarizes _why_ a customer is risky, _why_ intervention is recommended, and _what_ guardrails should be considered.

Main outputs:

- `results/customer_operational_explanations.csv`
- `results/customer_operational_explanations_summary.json`
- `results/customer_operational_explanations.md`

## AB Test

```bash
python src/main.py --mode abtest

```

## Simulation fidelity audit

```bash
python src/main.py --mode fidelity
```

Audits whether the simulator behaves **realistically** enough for downstream experimentation.
This includes treatment/control balance, funnel consistency, churn-risk alignment, and discount-pressure diagnostics.

Main outputs:

- `results/simulation_fidelity_summary.json`
- `results/simulation_fidelity_report.md`

## Realtime Bootstrap

```bash
python src/main.py --mode realtime-bootstrap
```

Initializes the real-time scoring environment before replaying or consuming streaming events.

This step typically prepares the required state, caches, intermediate artifacts, or message-stream resources so that the real-time pipeline can start from a consistent baseline.

## Realtime Replay

```bash
python src/main.py --mode realtime-replay --stream-limit 20000 --stream-max-events 20000
```

Replays simulated or stored customer events through **the real-time pipeline** so the system can update churn-risk-related outputs as if events were arriving live.

Parameter meaning:

`--stream-limit 20000`
Limits _how many_ customers or records are taken into the replay process.

`--stream-max-events 20000`
Limits the _total number_ of streamed events processed during replay.

## Detached Docker Run

```bash
docker compose up -d --build
```

Builds the Docker images and starts the services in detached mode, which means the containers run in the background.

Difference from:

```bash
docker compose up --build
```

`up --build`: runs in the foreground and shows logs directly in the terminal

`up -d --build`: runs in the background so you can continue using the terminal

## Implementation Order

```bash
python src/main.py --mode simulate
python src/main.py --mode features
python src/main.py --mode train
python src/main.py --mode survival
python src/main.py --mode uplift
python src/main.py --mode clv
python src/main.py --mode segment
python src/main.py --mode optimize --budget 50000000
python src/main.py --mode recommend --budget 5000000 --threshold 0.5 --max-customers 1000
python src/main.py --mode explain
python src/main.py --mode abtest
python src/main.py --mode fidelity
docker compose up -d --build
python src/main.py --mode realtime-bootstrap
python src/main.py --mode realtime-replay --stream-limit 20000 --stream-max-events 20000
```

## When You Want To Reimplement

```bash
python src/main.py --mode simulate --force --randomize
python src/main.py --mode features
python src/main.py --mode train
python src/main.py --mode survival
python src/main.py --mode uplift
python src/main.py --mode clv
python src/main.py --mode segment
python src/main.py --mode optimize --budget 50000000
python src/main.py --mode recommend --budget 5000000 --threshold 0.5 --max-customers 1000
python src/main.py --mode explain
python src/main.py --mode abtest
python src/main.py --mode fidelity
docker compose up -d --build
python src/main.py --mode realtime-bootstrap
python src/main.py --mode realtime-replay --stream-limit 20000 --stream-max-events 20000
```

## Reimplementation Flags

```bash
python src/main.py --mode simulate --force --randomize
```

This command is used when you want to **regenerate the simulation data** from scratch.

Parameter meaning:

`--force`

Overwrites existing generated files or reruns the simulation even if prior outputs already exist.

`--randomize`

Generates **a new randomized simulation** instead of reusing the same deterministic data configuration.

## User Live DB Mode

User Live DB Mode is the production-style path for uploaded company data. It initializes the static user artifacts into PostgreSQL live serving tables, then updates only changed customers when new customer events arrive.

Core flow:


1. Start Docker services.
2. Upload CSV and generate user artifacts from the dashboard.
3. The dashboard automatically seeds PostgreSQL live tables from `data/raw_user`, `data/feature_store_user`, and `results_user` after "매핑 확정 후 학습 시작" completes.
4. Ingest customer events.
5. Verify `feature_state`, `score`, `recommendation_candidates`, and `action_queue`.
6. Confirm the dashboard uses User Live DB results in 자사 데이터 mode.



### 1. Docker

```bash
docker compose up -d --build
```


### 2. Fixed E2E validation routine

Run the whole User Live DB smoke test:

```bash
./scripts/e2e_user_live_check.sh
```

Equivalent manual sequence:

```bash
# 1. live DB 상태 확인
curl -s "http://localhost:8000/api/v1/user-live/health" | python3 -m json.tool

# 2. seed 결과 확인
curl -s "http://localhost:8000/api/v1/user-live/seed-status" | python3 -m json.tool

# 3. 특정 고객 이벤트 발생
curl -X POST "http://localhost:8000/api/v1/user-live/events" \
  -H "Content-Type: application/json" \
  -d '{
    "customer_id": 1001,
    "event_type": "add_to_cart",
    "event_time": "2026-05-10T03:30:00+09:00",
    "amount": 35000,
    "source_event_id": "test-event-1001-001",
    "item_category": "fashion",
    "channel": "web",
    "raw_payload": {"test": true}
  }' | python3 -m json.tool


# 4. 해당 고객 feature_state 확인
curl -s "http://localhost:8000/api/v1/user-live/feature-state?customer_id=1001" | python3 -m json.tool

# 5. 해당 고객 score 확인
curl -s "http://localhost:8000/api/v1/user-live/scores?customer_id=1001" | python3 -m json.tool

# 6. 해당 고객 action_queue 확인
curl -s "http://localhost:8000/api/v1/user-live/actions?customer_id=1001" | python3 -m json.tool
```

All 6 checks should pass consistently before treating the User Live DB MVP as complete.


### Continuous mixed event injection for live demo

This script simulates both existing-customer behavior changes and new-customer acquisition.

- Existing customers are sampled from PostgreSQL `customer_scores`.
- New customers are assigned new `customer_id` values above the current maximum ID.
- Existing-customer events are sent to `/api/v1/user-live/events`.
- New-customer initial behavior sequences are sent to `/api/v1/user-live/events/batch`.
- Each event updates `customer_events`, `customer_feature_state`, `customer_scores`, `recommendation_candidates`, and `action_queue` when scoring/action flags are enabled.

Run this in a separate terminal:

```bash
bash scripts/live_demo_mixed_events.sh
```

### 3. Dashboard verification points

In 자사 데이터 mode, verify these before a PR or presentation:

1. Dashboard reads PostgreSQL `customer_scores` before CSV results.
2. Posting one event changes only the corresponding `customer_id` score path.
3. `action_queue` count increases or the existing row is updated.
4. Refreshing the dashboard preserves values from PostgreSQL.
5. Simulator mode and user mode do not mix. User mode must not fall back to `results/` or `data/raw` simulator artifacts.


Summary:

```markdown
## Summary
- Add PostgreSQL-backed user live mode
- Seed live tables from uploaded user artifacts
- Ingest customer events into customer_events
- Update customer_feature_state incrementally
- Re-score changed customers only
- Refresh recommendation_candidates and action_queue
- Expose user-live API endpoints for dashboard integration

## Validation
- Checked /user-live/health
- Seeded user artifacts into PostgreSQL
- Posted customer event for customer_id=1001
- Verified updated scores and action queue records
```
