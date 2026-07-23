# Angular security guidance and audit checklist

Use this for auth, authorization, RBAC, XSS, CSRF, CSP, secure storage, route protection, and handling sensitive data in the frontend.

## Default position

- Treat frontend authorization as UX gating, not a security boundary.
- Enforce authorization on the API.
- Prefer HttpOnly, Secure, SameSite cookies for session tokens when the backend supports them.
- Use Angular binding instead of manual DOM writes.
- Avoid `bypassSecurityTrust*` unless the source is trusted and sanitized upstream.

## Required design checks

- Authentication flow, refresh strategy, logout behavior, and session expiry.
- Route guards for navigation and API authorization.
- RBAC or ABAC mapping from claims to UI affordances.
- CSRF protection for cookie-based auth.
- CSP policy for script, style, frame, image, and connect sources.
- Sensitive data handling in logs, storage, errors, and analytics.

## Security audit checklist

### Authentication

- Token/session storage should match the app risk.
- Logout should clear client state and invalidate server sessions when needed.
- Refresh flows should handle expiry, replay, and failure.

### Authorization

- The API should enforce data access rules.
- Route guards and UI checks should match server permissions.
- Denied states should be tested and user-friendly.

### Browser security

- Don’t use `bypassSecurityTrust*` unless the source is trusted.
- Sanitize rich text at ingestion and when rendering.
- Define CSP and make it compatible with third-party scripts.
- Use HttpOnly, Secure, SameSite cookies plus CSRF protection for cookie auth.

### Data handling

- Don’t log or persist sensitive values.
- Don’t send secrets to analytics.
- Don’t leak internals in error messages.
- Review third-party widgets for script injection and CSP issues.

## Production warnings

- Don’t rely on hidden buttons for authorization.
- Don’t store long-lived bearer tokens in local storage for high-risk apps.
- Sanitize rich text before rendering.
- Review third-party widgets for script injection and CSP compatibility.
