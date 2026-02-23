# Runtime Logs Tool

Query agent runtime logs at three levels of detail — from high-level run summaries down to individual tool call inputs and outputs. Useful for debugging failed or degraded graph runs without leaving the agent session.

## Tools

The three tools are designed to be used in sequence: start broad with `query_runtime_logs`, narrow down with `query_runtime_log_details`, then inspect specifics with `query_runtime_log_raw`.

---

### `query_runtime_logs`

**Level 1** — High-level pass/fail summaries for recent graph runs.

| Parameter        | Type    | Required | Description                                                        |
| ---------------- | ------- | -------- | ------------------------------------------------------------------ |
| `agent_work_dir` | string  | Yes      | Path to the agent's working directory                              |
| `status`         | string  | No       | Filter by status: `success`, `failure`, `degraded`, `in_progress`, `needs_attention`. Empty = all. |
| `limit`          | integer | No       | Max results to return (default: 20)                                |

**Status values:**

| Status            | Meaning                                      |
| ----------------- | -------------------------------------------- |
| `success`         | All nodes completed without issues           |
| `failure`         | One or more nodes failed                     |
| `degraded`        | Completed with warnings or partial failures  |
| `in_progress`     | Run is still executing (no summary yet)      |
| `needs_attention` | Flagged runs requiring investigation         |

---

### `query_runtime_log_details`

**Level 2** — Per-node completion details for a specific run.

| Parameter             | Type    | Required | Description                                              |
| --------------------- | ------- | -------- | -------------------------------------------------------- |
| `agent_work_dir`      | string  | Yes      | Path to the agent's working directory                    |
| `run_id`              | string  | Yes      | Run ID from `query_runtime_logs` results                 |
| `needs_attention_only`| boolean | No       | If `true`, only return flagged nodes (default: `false`)  |
| `node_id`             | string  | No       | Filter to a specific node ID                             |

**Returns:** Per-node success/failure, exit status, verdict counts, and attention flags.

---

### `query_runtime_log_raw`

**Level 3** — Full tool call inputs/outputs and LLM step details for a specific run.

| Parameter        | Type    | Required | Description                                                    |
| ---------------- | ------- | -------- | -------------------------------------------------------------- |
| `agent_work_dir` | string  | Yes      | Path to the agent's working directory                          |
| `run_id`         | string  | Yes      | Run ID from `query_runtime_logs` results                       |
| `step_index`     | integer | No       | Specific step index to retrieve, or `-1` for all (default: -1) |
| `node_id`        | string  | No       | Filter to steps belonging to a specific node                   |

**Returns:** Tool inputs/outputs, LLM text, and token counts per step.

---

## Typical Debugging Workflow

```python
# 1. Find failed runs
query_runtime_logs(agent_work_dir="/path/to/agent", status="needs_attention")

# 2. Drill into a specific run to find which node failed
query_runtime_log_details(
    agent_work_dir="/path/to/agent",
    run_id="session_abc123",
    needs_attention_only=True,
)

# 3. Inspect the raw tool calls for that node
query_runtime_log_raw(
    agent_work_dir="/path/to/agent",
    run_id="session_abc123",
    node_id="my_failing_node",
)
```

## Credentials

No API key required. Reads from local log files on disk.

## Log File Locations

| Level | File                                              |
| ----- | ------------------------------------------------- |
| L1    | `sessions/{session_id}/logs/summary.json`         |
| L2    | `sessions/{session_id}/logs/details.jsonl`        |
| L3    | `sessions/{session_id}/logs/tool_logs.jsonl`      |

Deprecated location (`runtime_logs/runs/{run_id}/`) is also supported for backwards compatibility.
