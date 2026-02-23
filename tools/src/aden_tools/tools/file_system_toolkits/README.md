# File System Toolkits

Core file system and data persistence tools for agents. Provides two groups of tools: **file operations** for reading, writing, and manipulating files within the session sandbox, and **data tools** for structured persistence across agent steps.

All file paths are sandboxed per `workspace_id` / `agent_id` / `session_id` to prevent path traversal.

## File Operation Tools

### `view_file`

Read the contents of a file.

### `write_to_file`

Write content to a file, creating it if it does not exist.

### `list_dir`

List the contents of a directory.

### `replace_file_content`

Replace specific content within an existing file.

### `apply_diff`

Apply a diff patch to a file.

### `apply_patch`

Apply a unified patch to a file.

### `grep_search`

Search file contents using a regex pattern.

### `execute_command_tool`

Execute a shell command within the session sandbox.

---

## Data Persistence Tools

These tools persist structured data across agent steps within a session, using a dedicated data directory.

### `save_data`

Save a named data file.

| Parameter  | Type   | Required | Description              |
| ---------- | ------ | -------- | ------------------------ |
| `filename` | string | Yes      | Name of the data file    |
| `data`     | string | Yes      | Content to save          |
| `data_dir` | string | Yes      | Target data directory    |

### `load_data`

Load a previously saved data file.

| Parameter  | Type   | Required | Description              |
| ---------- | ------ | -------- | ------------------------ |
| `filename` | string | Yes      | Name of the data file    |
| `data_dir` | string | Yes      | Data directory to read from |

### `list_data_files`

List all persisted data files in the session's data directory.

| Parameter  | Type   | Required | Description           |
| ---------- | ------ | -------- | --------------------- |
| `data_dir` | string | Yes      | Data directory to list |

### `serve_file_to_user`

Serve a file for the user to download.

### `append_data`

Append content to an existing data file.

| Parameter  | Type   | Required | Description                        |
| ---------- | ------ | -------- | ---------------------------------- |
| `filename` | string | Yes      | Name of the data file              |
| `data`     | string | Yes      | Content to append                  |
| `data_dir` | string | Yes      | Data directory                     |

### `edit_data`

Replace specific text within a data file.

| Parameter  | Type   | Required | Description                        |
| ---------- | ------ | -------- | ---------------------------------- |
| `filename` | string | Yes      | Name of the data file              |
| `old_text` | string | Yes      | Text to find and replace           |
| `new_text` | string | Yes      | Replacement text                   |
| `data_dir` | string | Yes      | Data directory                     |

---

## Credentials

No API key required.

## Security

All paths are resolved through a three-layer sandbox (`workspace_id/agent_id/session_id`) defined in `security.py`. Path traversal attempts (e.g. `../../`) are blocked.
