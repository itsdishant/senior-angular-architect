# Angular Security Audit Checklist

## Authentication

- Token/session storage matches application risk.
- Logout clears client state and invalidates server session when applicable.
- Refresh flows handle expiry, replay, and failure.

## Authorization

- API enforces data access rules.
- Route guards and UI checks match server-side permissions.
- Denied states are tested and user-readable.

## Browser Security

- No unnecessary `bypassSecurityTrust*` usage.
- Rich text is sanitized at ingestion and rendering.
- CSP is defined and compatible with third-party scripts.
- Cookie auth uses HttpOnly, Secure, SameSite, and CSRF protection.

## Data Handling

- Sensitive values are not logged, persisted, or sent to analytics.
- Error messages do not leak internals.
- Dependencies and third-party widgets are reviewed for known risks.

