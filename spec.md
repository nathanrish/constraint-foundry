# Constraint Foundry — Technical Specification

## 1. Repository Rules

- Only files under `src/` and `tests/` may be modified by the harness.
- `design.md`, `spec.md`, `test-case.md`, `prompt.md`,
  `harness.yaml`, and `control-plane.yaml` are immutable.
- No file deletions allowed.
- No external network calls from harness.

---

## 2. Core Modules

### 2.1 state_registry.py

Class: StateRegistry

Responsibilities:
- Persist build state to build_state.json
- Track completed prompts
- Track retry counts
- Return next incomplete prompt
- Maintain total iteration count

Required Methods:
- load() -> dict
- save() -> None
- mark_completed(prompt_id: str) -> None
- increment_retry(prompt_id: str) -> None
- get_retry_count(prompt_id: str) -> int
- is_completed(prompt_id: str) -> bool
- get_next_prompt(prompt_ids: list[str]) -> str | None

Constraints:
- Must handle missing file
- Must handle corrupted JSON safely
- Must never silently fail

---

### 2.2 prompt_loader.py

Class: PromptLoader

Responsibilities:
- Parse prompt.md
- Extract ordered list of atomic prompts

Required Output Format:
[
  {
    "id": "P-001",
    "content": "...",
    "metadata": {}
  }
]

Constraints:
- Deterministic parsing
- Fail on malformed structure

---

### 2.3 agent_executor.py

Class: AgentExecutor

Responsibilities:
- Encapsulate LLM invocation
- Accept prompt + failure context
- Return generated code as string

Constraints:
- No filesystem writes
- Must be mockable
- Must log execution metadata

---

### 2.4 quality_gate.py

Class: QualityGate

Responsibilities:
- Run pytest
- Run pylint
- Parse structured outputs
- Evaluate against thresholds from harness.yaml

Required Output Format:
{
  "coverage": float,
  "pylint_score": float,
  "passed": bool
}

---

### 2.5 harness_runner.py

Class: HarnessRunner

Responsibilities:
- Load harness.yaml
- Load prompts
- Iterate sequentially
- Skip completed prompts
- Execute prompt cycle
- Enforce retry limits
- Enforce total iteration limits
- Persist state
- Emit structured logs

Execution Loop:

For each prompt:
1. Execute agent
2. Apply code
3. Run tests
4. Run lint
5. If fail → retry (bounded)
6. If pass → mark completed

Stop Conditions:
- All prompts complete
- Prompt exceeds max retries
- Total iterations exceeded

---

### 2.6 logger.py

Class: StructuredLogger

Responsibilities:
- Emit JSON logs with:
  - timestamp
  - event_type
  - payload
- No plain print statements
