# Angular Security Guidance and Audit Checklist

Use this for authentication, authorization, RBAC, XSS, CSRF, CSP, secure storage, route protection, and frontend handling of sensitive data.

## Default Position

- Treat frontend authorization as UX gating, not a security boundary.
- Enforce authorization on the API.
- Prefer HttpOnly, Secure, SameSite cookies for session tokens when the backend supports them.
- Use Angular template binding instead of manual DOM writes.
- Avoid `bypassSecurityTrust*` unless the source is trusted and sanitized upstream.

## Required Design Checks

- Authentication flow, refresh strategy, logout behavior, and session expiry.
- Route guards for navigation and API authorization for data access.
- RBAC or ABAC mapping from claims to UI affordances.
- CSRF protection for cookie-based auth.
- CSP policy for script, style, frame, image, and connect sources.
- Sensitive data handling in logs, local storage, session storage, errors, and analytics.

## Security Audit Checklist

### Authentication

- Token/session storage matches application risk.
- Logout clears client state and invalidates server session when applicable.
- Refresh flows handle expiry, replay, and failure.

### Authorization

- API enforces data access rules.
- Route guards and UI checks match server-side permissions.
- Denied states are tested and user-readable.

### Browser Security

- No unnecessary `bypassSecurityTrust*` usage.
- Rich text is sanitized at ingestion and rendering.
- CSP is defined and compatible with third-party scripts.
- Cookie auth uses HttpOnly, Secure, SameSite, and CSRF protection.

### Data Handling

- Sensitive values are not logged, persisted, or sent to analytics.
- Error messages do not leak internals.
- Dependencies and third-party widgets are reviewed for known risks.

## Production Warnings

- Never rely on hidden buttons as authorization.
- Never store long-lived bearer tokens in local storage for high-risk apps.
- Sanitize rich text at ingestion and rendering.
- Review third-party widgets for script injection and CSP compatibility.
