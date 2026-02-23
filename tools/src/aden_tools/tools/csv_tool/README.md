# CSV Tool

Read, write, and query CSV files within the agent's session sandbox. All file paths are relative to the session sandbox and are sandboxed per `workspace_id` / `agent_id` / `session_id`.

## Tools

### `csv_read`

Read rows from a CSV file.

| Parameter    | Type    | Required | Description                              |
| ------------ | ------- | -------- | ---------------------------------------- |
| `path`       | string  | Yes      | Path to the CSV file (relative to sandbox) |
| `workspace_id` | string | Yes     | Workspace identifier                     |
| `agent_id`   | string  | Yes      | Agent identifier                         |
| `session_id` | string  | Yes      | Session identifier                       |
| `limit`      | integer | No       | Max rows to return (default: all)        |
| `offset`     | integer | No       | Rows to skip from the start (default: 0) |

---

### `csv_write`

Write a new CSV file. Overwrites if the file already exists.

| Parameter    | Type         | Required | Description                              |
| ------------ | ------------ | -------- | ---------------------------------------- |
| `path`       | string       | Yes      | Path to the CSV file                     |
| `workspace_id` | string     | Yes      | Workspace identifier                     |
| `agent_id`   | string       | Yes      | Agent identifier                         |
| `session_id` | string       | Yes      | Session identifier                       |
| `columns`    | list[string] | Yes      | Column names for the header row          |
| `rows`       | list[dict]   | Yes      | Rows to write, each as a key-value dict  |

---

### `csv_append`

Append rows to an existing CSV file. The file must already exist with a header row.

| Parameter    | Type       | Required | Description                              |
| ------------ | ---------- | -------- | ---------------------------------------- |
| `path`       | string     | Yes      | Path to the CSV file                     |
| `workspace_id` | string   | Yes      | Workspace identifier                     |
| `agent_id`   | string     | Yes      | Agent identifier                         |
| `session_id` | string     | Yes      | Session identifier                       |
| `rows`       | list[dict] | Yes      | Rows to append, keys must match existing columns |

---

### `csv_info`

Get metadata about a CSV file without reading all its data.

| Parameter    | Type   | Required | Description          |
| ------------ | ------ | -------- | -------------------- |
| `path`       | string | Yes      | Path to the CSV file |
| `workspace_id` | string | Yes    | Workspace identifier |
| `agent_id`   | string | Yes      | Agent identifier     |
| `session_id` | string | Yes      | Session identifier   |

**Returns:** Column names, column count, total row count, and file size in bytes.

---

### `csv_sql`

Query a CSV file using SQL, powered by DuckDB. The CSV is loaded as a table named `data`. Only `SELECT` statements are permitted.

| Parameter    | Type   | Required | Description                                    |
| ------------ | ------ | -------- | ---------------------------------------------- |
| `path`       | string | Yes      | Path to the CSV file                           |
| `workspace_id` | string | Yes    | Workspace identifier                           |
| `agent_id`   | string | Yes      | Agent identifier                               |
| `session_id` | string | Yes      | Session identifier                             |
| `query`      | string | Yes      | SQL SELECT query. The CSV is available as `data`. |

**Example queries:**

```sql
-- Filter rows
SELECT * FROM data WHERE status = 'pending'

-- Aggregate
SELECT category, COUNT(*) as count, AVG(price) as avg_price FROM data GROUP BY category

-- Sort and limit
SELECT name, price FROM data ORDER BY price DESC LIMIT 5

-- Case-insensitive search
SELECT * FROM data WHERE LOWER(name) LIKE '%phone%'
```

## Credentials

No API key required.

## Dependencies

`csv_sql` requires DuckDB:

```bash
uv pip install duckdb
# or
uv pip install "tools[sql]"
```

All other tools use Python's built-in `csv` module.

## Notes

- All files must have a `.csv` extension.
- Files are encoded as UTF-8.
- `csv_sql` only allows `SELECT` statements — `INSERT`, `UPDATE`, `DELETE`, `DROP`, and similar operations are blocked.
- Use `csv_write` to create a new file, then `csv_append` to add rows incrementally.
