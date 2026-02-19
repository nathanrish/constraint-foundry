# Constraint Foundry — Test Contract

## Global Requirements

- pylint >= 9.5
- coverage >= 90%
- critical modules >= 95% coverage:
  - state_registry.py
  - harness_runner.py
  - quality_gate.py

Coverage must be generated via:

pytest --cov=src --cov-report=json

---

## Module Test Requirements

### StateRegistry
- test_initializes_file_if_missing
- test_mark_completed
- test_retry_increment
- test_get_next_prompt
- test_persistence
- test_corrupt_json_recovery

---

### PromptLoader
- test_parses_prompt_ids
- test_preserves_order
- test_invalid_structure_fails

---

### AgentExecutor
- test_execute_returns_string
- test_accepts_failure_context
- test_no_file_side_effects

---

### QualityGate
- test_parse_coverage
- test_parse_pylint
- test_evaluate_pass
- test_evaluate_fail

---

### HarnessRunner
- test_load_config
- test_prompt_execution_success
- test_retry_logic
- test_stop_after_max_retries
- test_total_iteration_limit
- test_skip_completed_prompt
- test_state_updates
- test_only_writable_paths_modified

---

## Integration Tests

- test_full_run_success
- test_full_run_failure
- test_halts_when_all_complete

---

## Logging Tests

All major events must emit structured JSON:
- AGENT_EXECUTION
- TEST_RESULT
- LINT_RESULT
- RETRY
- PROMPT_COMPLETE
- SYSTEM_EXIT
