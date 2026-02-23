# Account Info Tool

Allows agents to query which external accounts are connected at runtime, without needing to hard-code provider details.

## Tools

### `get_account_info`

List connected accounts and their identities.

| Parameter  | Type   | Required | Description                                      |
| ---------- | ------ | -------- | ------------------------------------------------ |
| `provider` | string | No       | Filter by provider type (e.g. `"google"`). Omit to return all accounts. |

**Returns:** Account IDs, provider types, and identity labels (email, username, workspace) for each connected account.

**Example:**

```python
# List all connected accounts
get_account_info()

# Filter to Google accounts only
get_account_info(provider="google")
```

**Example response:**

```json
{
  "accounts": [
    {
      "id": "acc_123",
      "provider": "google",
      "identity": "user@example.com"
    }
  ],
  "count": 1
}
```

## Credentials

No API key required. Reads from the agent's configured credential store.

## Notes

- Returns an empty list if no credential store is configured.
- Useful for agents that need to select or display the correct account before taking action (e.g. choosing which Gmail account to send from).
