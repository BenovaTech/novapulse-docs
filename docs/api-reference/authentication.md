---
title: Authentication
description: How to authenticate with the NovaPulse API using API keys.
---
 
# Authentication
 
The NovaPulse API uses **API key authentication**. Include your API key
as a Bearer token in the `Authorization` header of every request.
 
---
 
## Obtain an API key
 
1. Log in to the [NovaPulse dashboard](https://app.novapulse.io).
2. Navigate to **Settings → API Keys**.
3. Click **Generate new key**.
4. Copy the key immediately, it is shown only once.
 
!!! warning "Keep your API key secure"
    Never commit API keys to source control or expose them in
    client-side code. Use environment variables or a secrets manager.
 
---
 
## Authentication header
 
Include your key in every API request:
 
```bash
Authorization: Bearer nvp_live_YOUR_API_KEY_HERE
```
 
---
 
## Request examples
 
=== "cURL"
    ```bash
    curl -X GET https://api.novapulse.io/v1/jobs \
      -H 'Authorization: Bearer nvp_live_abc123...' \
      -H 'Content-Type: application/json'
    ```
 
=== "Python"
    ```python
    import requests
 
    headers = {
        'Authorization': 'Bearer nvp_live_abc123...',
        'Content-Type': 'application/json'
    }
 
    response = requests.get('https://api.novapulse.io/v1/jobs', headers=headers)
    ```
 
=== "JavaScript"
    ```javascript
    const response = await fetch('https://api.novapulse.io/v1/jobs', {
      headers: {
        'Authorization': 'Bearer nvp_live_abc123...',
        'Content-Type': 'application/json'
      }
    });
    ```
 
---
 
## Authentication errors
 
| HTTP status | Error code | Description |
|---|---|---|
| `401 Unauthorized` | `invalid_api_key` | The key is missing or malformed. |
| `401 Unauthorized` | `expired_api_key` | The key has been revoked or expired. |
| `403 Forbidden` | `insufficient_permissions` | The key doesn't have access to this resource. |
 
---
 
## API key prefixes
 
NovaPulse API keys use a prefix to distinguish environment:
 
| Prefix | Environment | Use for |
|---|---|---|
| `nvp_live_` | Production | Live data and real integrations |
| `nvp_test_` | Sandbox | Development and testing — no charges |
 
---
 
## Related
 
- [Rate limits](../reference/rate-limits.md)
- [Error codes reference](../reference/error-codes.md)
- [Getting started tutorial](../tutorials/getting-started.md)



