# HubSpot Tool

Manage contacts, companies, and deals in HubSpot CRM via the [HubSpot API v3](https://developers.hubspot.com/docs/api/crm).

## Tools

### Contacts

| Tool                       | Description                        |
| -------------------------- | ---------------------------------- |
| `hubspot_search_contacts`  | Search contacts by name, email, phone, etc. |
| `hubspot_get_contact`      | Get a contact by ID                |
| `hubspot_create_contact`   | Create a new contact               |
| `hubspot_update_contact`   | Update an existing contact         |

### Companies

| Tool                        | Description                         |
| --------------------------- | ----------------------------------- |
| `hubspot_search_companies`  | Search companies by name, domain, etc. |
| `hubspot_get_company`       | Get a company by ID                 |
| `hubspot_create_company`    | Create a new company                |
| `hubspot_update_company`    | Update an existing company          |

### Deals

| Tool                    | Description                      |
| ----------------------- | -------------------------------- |
| `hubspot_search_deals`  | Search deals by name, stage, etc. |
| `hubspot_get_deal`      | Get a deal by ID                 |
| `hubspot_create_deal`   | Create a new deal                |
| `hubspot_update_deal`   | Update an existing deal          |

---

## Common Parameters

All tools accept an optional `account` parameter to select a specific HubSpot account when multiple are configured in the credential store.

Search tools accept:

| Parameter    | Type         | Required | Description                                   |
| ------------ | ------------ | -------- | --------------------------------------------- |
| `query`      | string       | No       | Full-text search query                        |
| `properties` | list[string] | No       | Properties to return (defaults vary per object) |
| `limit`      | integer      | No       | Max results, 1–100 (default: 10)              |

Get/update tools require the object ID (`contact_id`, `company_id`, `deal_id`).

Create/update tools accept a `properties` dict matching HubSpot property names.

---

## Common Property Names

**Contacts:** `email`, `firstname`, `lastname`, `phone`, `company`

**Companies:** `name`, `domain`, `industry`, `city`, `country`

**Deals:** `dealname`, `amount`, `dealstage`, `closedate`, `pipeline`

---

## Credentials

| Variable               | Description                        |
| ---------------------- | ---------------------------------- |
| `HUBSPOT_ACCESS_TOKEN` | HubSpot Private App access token   |

Get your token from [developers.hubspot.com](https://developers.hubspot.com/) → Apps → Private Apps.

The tool also supports OAuth2 tokens via the credential store.

## Notes

- Rate limits: HubSpot enforces per-second and per-day limits. A `429` error means you've hit the limit — wait and retry.
- Scopes required: ensure your Private App has `crm.objects.contacts`, `crm.objects.companies`, and `crm.objects.deals` read/write scopes enabled.
