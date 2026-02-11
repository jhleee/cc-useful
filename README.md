# cc-useful

A collection of practical skills for Claude Code.

## Skills

### critical-acceptance (비판적수용)

피드백을 무조건 수용하거나 거부하지 않고, 객관적 원칙에 기반하여 비판적으로 수용한다.

- **Accept**: 객관적 원칙(Clean Code, SOLID, Architecture)에 부합하고 실질적 개선이 있는 피드백
- **Reject**: 원칙에 위배되거나 주관적 선호에 기반한 피드백 — 근거와 함께 거부
- **Negotiate**: 부분적으로 타당하거나 대안이 존재할 때 — 대안 제시 후 논의

6단계 평가 프로세스: 피드백 이해 → 코드 분석 → 원칙 평가 → 피드백 검증 → 사용자 의도 확인 → 판단 및 응답

### backend-handoff

Generate structured integration documents from backend code for frontend AI agents.

- Analyzes backend code (routes, models, middleware, config) across any framework
- Outputs a Markdown spec with API endpoints, TypeScript data models, auth flow, error handling, and pagination
- Targets AI agents as readers — goal-oriented, unambiguous, immediately actionable

4-phase workflow: Code Discovery → User Feedback → Document Generation → Validation

## Installation

```bash
claude plugin add -- jhleee/cc-useful
```

## Usage

스킬은 Claude가 관련 컨텍스트를 감지하면 자동으로 트리거됩니다.

- 코드 리뷰 피드백을 평가하는 상황 → **critical-acceptance** 스킬 자동 로드
- 백엔드 코드를 프론트엔드용으로 문서화하는 상황 → **backend-handoff** 스킬 자동 로드

## Author

jhleee
