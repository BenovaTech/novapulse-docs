---
title: Security and Compliance
description: How NovaPulse protects data, handles PII, and supports GDPR compliance.
---

# Security and Compliance

NovaPulse is designed with data protection as a foundational requirement,
not an afterthought. This page describes the security controls, PII
handling, and compliance posture of the platform.

---

## Encryption

| Data state | Standard | Details |
|---|---|---|
| In transit | TLS 1.3 | All API communication is encrypted. HTTP requests are rejected. |
| At rest | AES-256 | Audio files, transcripts, vectors, and insights are encrypted at rest. |
| Keys | Cloud KMS | Encryption keys are managed by a cloud key management service with automatic rotation. |

---

## PII detection and redaction

NovaPulse automatically detects and redacts personally identifiable
information from all transcripts before they are stored or returned
via the API.

### What is detected

| Entity type | Examples |
|---|---|
| Full names | "My name is Sarah Johnson" |
| Phone numbers | "+34 612 345 678", "(555) 123-4567" |
| Email addresses | "contact@example.com" |
| Account numbers | "Account 4821-XXXX" |
| Dates of birth | "born on March 12, 1985" |
| Physical addresses | "123 Main Street, Madrid" |

### How redaction works

Detected PII is replaced with a typed placeholder in the stored transcript:

```
Original:  "My name is Sarah Johnson, my account number is 48219821."
Redacted:  "My name is [NAME], my account number is [ACCOUNT_NUMBER]."
```

Redaction is applied before the transcript is written to storage.
By default, all API responses return the redacted version.

### Accessing unredacted transcripts

Unredacted transcripts are available to API keys with the `pii:read`
permission scope. This scope must be explicitly granted in the dashboard
and should be restricted to authorized compliance workflows only.

```bash
curl "https://api.novapulse.io/v1/transcriptions/job_id?redact_pii=false" \
  -H "Authorization: Bearer nvp_live_KEY_WITH_PII_READ_SCOPE"
```

!!! warning "Handle unredacted data carefully"
    Unredacted transcripts contain personal data. Ensure your integration
    stores and transmits this data in compliance with applicable data
    protection regulations.

---

## Multi-tenant isolation

NovaPulse serves multiple customers from shared infrastructure. Data
isolation is enforced at every layer:

- **API layer:** API keys are scoped to a single tenant. No cross-tenant
  request path exists.
- **Storage layer:** Row-level security ensures that database queries
  only return records belonging to the authenticated tenant.
- **Processing layer:** Jobs are processed in isolated compute contexts.
  No data is shared between tenants during processing.

---

## Data retention

| Data type | Default retention | Configurable |
|---|---|---|
| Audio files | 90 days | Yes, per account |
| Transcripts | 90 days | Yes, per account |
| Insights and vectors | 90 days | Yes, per account |
| Job metadata | 12 months | No |

After the retention period, data is permanently deleted and cannot
be recovered. Configure retention periods in the dashboard under
Settings > Data Retention.

---

## GDPR compliance

NovaPulse is designed to support GDPR compliance for customers processing
personal data of EU residents.

| Requirement | NovaPulse implementation |
|---|---|
| Lawful basis for processing | Customers are responsible for establishing lawful basis. NovaPulse processes data on behalf of the customer as a data processor. |
| Data processing agreement (DPA) | Available on request for all paid plans. |
| Right to erasure | Supported via the job deletion API. Deleting a job permanently removes all associated audio, transcript, and insight data. |
| Data minimization | PII redaction is enabled by default. Unredacted access requires explicit permission. |
| Data location | Data is stored in the EU region by default. US and APAC regions available on Enterprise plans. |
| Sub-processors | A current list of NovaPulse sub-processors is available on request. |

---

## Authentication security

- API keys use a prefixed format (`nvp_live_` or `nvp_test_`) to prevent
  accidental use of production keys in test environments.
- Keys are shown only once at creation. NovaPulse does not store the
  full key value after creation.
- Keys can be revoked instantly from the dashboard.
- All API key usage is logged with timestamp, endpoint, and IP address.

---

## Responsible disclosure

To report a security vulnerability in NovaPulse, contact
security@novapulse.io with a description of the issue.
Do not disclose vulnerabilities publicly before they have been addressed.

---

## Related

- [System Overview](system-overview.md)
- [Data Flow](data-flow.md)
- [Authentication](../api-reference/authentication.md)
- [Error Codes](../reference/error-codes.md)

