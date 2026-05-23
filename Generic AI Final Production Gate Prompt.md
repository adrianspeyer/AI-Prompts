# AI Final Production Gate Prompt for Public Code Projects

Use this prompt when a code project is believed to be nearly ready for production. It is intentionally generic and project-neutral. Paste this into an AI coding assistant, attach or point it to the full repository, and ask it to perform a final production-readiness pass.

---

## Prompt: Final Production Gate Review

You are acting as a senior production-readiness reviewer, security-minded engineer, accessibility reviewer, SEO technical auditor, QA lead, and pragmatic product/UX reviewer.

Your job is **not** to praise the project or assume it is ready. Your job is to identify what could break, mislead users, hurt SEO, create security risk, fail accessibility standards, damage performance, or cause deployment issues.

Review the full codebase, configuration, documentation, routes, UI, API endpoints, forms, build scripts, tests, environment handling, and deployment assumptions.

Do **not** rewrite the entire project unless asked. Produce a clear, prioritized audit and, where safe, propose or implement focused fixes.

---

# 1. Operating Rules

## Be strict

Do not mark something as production-ready unless the code proves it.

If a feature is only mocked, local-only, demo-only, hardcoded, or dependent on missing credentials, label it clearly.

Use these status labels:

```text
PASS
WARNING
BLOCKER
NEEDS VERIFICATION
DEFERRED
NOT APPLICABLE
```

## Do not hide uncertainty

If something cannot be verified from the repository alone, say so.

Examples:

```text
NEEDS VERIFICATION: Live email delivery cannot be verified without production credentials.
NEEDS VERIFICATION: Payment webhook signing cannot be verified without deployed endpoint access.
WARNING: Robots.txt exists, but indexing behavior must be confirmed after deployment.
```

## Separate audit from implementation

Return your answer in this structure:

```text
1. Executive Production Summary
2. Launch Blockers
3. High-Risk Warnings
4. Recommended Fix Order
5. Detailed Audit by Category
6. Test Plan
7. Security Review
8. SEO / Public Web Review
9. Accessibility Review
10. UX / Product Quality Review
11. Deployment Checklist
12. Final Go / No-Go Recommendation
13. Optional Patch Plan
```

If asked to make changes, implement them in small, reviewable commits or clearly described patch groups.

---

# 2. Core Production Readiness Questions

Answer these directly:

```text
Can this project be safely deployed publicly today?
Can users complete the core flow without developer intervention?
Are there any exposed secrets, debug routes, mock-only features, or misleading UI states?
Are forms, APIs, and uploads protected against basic abuse?
Is the site accessible enough for a public launch?
Is SEO technically sound for public pages?
Are errors handled gracefully?
Are analytics, logging, monitoring, and rollback plans defined?
Are tests meaningful or just superficial?
What would you refuse to ship?
```

---

# 3. Repository Hygiene Review

Check for:

- Dead files
- Duplicate components
- Stale docs
- Broken imports
- Commented-out production code
- Console logs in production paths
- Debug routes
- Mock-only data presented as real
- Hardcoded credentials or secrets
- Hardcoded domains, emails, tokens, or internal references
- Unused dependencies
- Vulnerable dependencies
- Overly broad permissions
- Missing `.env.example`
- Missing deployment instructions
- Missing README setup path
- Missing license, if public or open source

Recommended checks:

```bash
npm install
npm run build
npm run lint
npm run typecheck
npm test
npm audit
```

If the project uses another stack, adapt commands accordingly.

---

# 4. SEO Readiness for Public Projects

If the project has public web pages, audit SEO seriously.

## Technical SEO

Check for:

- Unique page titles
- Unique meta descriptions
- Canonical URLs
- Proper `robots.txt`
- Proper `sitemap.xml`
- No accidental `noindex`
- No staging URLs in metadata
- Open Graph metadata
- Twitter/X card metadata
- Valid favicons and app icons
- Structured data where relevant
- Crawlable public content
- Server-rendered or pre-rendered critical public pages where appropriate
- Correct status codes for missing pages
- Custom 404 page
- Redirect handling
- Pagination handling, if applicable
- Duplicate-content risks
- Internal linking
- Image alt text
- Descriptive URLs
- Clean heading hierarchy

## Content SEO

Check for:

- Clear H1 on each public page
- Search-intent alignment
- Avoiding thin placeholder pages
- No lorem ipsum or fake public content
- Meaningful landing page copy
- Strong page-specific summaries
- Descriptive CTAs
- Local SEO support if relevant
- Organization, product, article, event, or breadcrumb schema if relevant

## SEO Output

Produce a table:

```text
Page / Route
Indexable?
Title Present?
Meta Description Present?
H1 Present?
Canonical Present?
OG Image Present?
Structured Data?
Issues
Priority
```

---

# 5. Accessibility Review

Use WCAG 2.2 AA as the target unless the project specifies another standard.

Check for:

- Keyboard navigation
- Focus states
- Skip links where appropriate
- Semantic HTML
- Correct heading order
- Button vs link correctness
- Form labels
- Error messages tied to inputs
- ARIA only where needed
- Color contrast
- Visible focus indicators
- Alt text for informative images
- Empty alt text for decorative images
- Modal focus trapping
- Escape-to-close behavior
- Screen-reader-friendly status messages
- Reduced motion support
- Touch target size
- Mobile accessibility
- Captions/transcripts for media if relevant
- No keyboard traps

Recommended tools:

```text
axe
Lighthouse Accessibility
WAVE
Screen reader spot check
Keyboard-only navigation test
```

Accessibility output:

```text
Issue
WCAG Principle
Affected Component / Route
Severity
Fix Recommendation
```

---

# 6. UI / UX Production Review

Review whether the interface feels finished, understandable, and trustworthy.

Check for:

- Clear primary user journey
- No dead-end screens
- No fake buttons
- No confusing demo-only messages on public pages
- Empty states
- Loading states
- Error states
- Success states
- Confirmation states
- Mobile responsiveness
- Tablet responsiveness
- Consistent spacing
- Consistent typography
- Consistent button hierarchy
- Clear navigation
- Breadcrumbs where useful
- Helpful form validation
- Clear pricing or plan language, if applicable
- No misleading claims
- No inaccessible animations
- No broken image states
- No layout shifts that harm usability
- No internal jargon exposed to users

Explicitly identify:

```text
Top 5 UX issues that would make users lose trust
Top 5 missing states
Top 5 confusing labels or flows
```

---

# 7. Security Review

Perform a security-minded review. Do not assume the framework protects everything.

## Authentication and Authorization

Check for:

- Protected routes are actually protected server-side
- Role checks are enforced server-side, not only in the UI
- Admin routes require admin permissions
- Users cannot self-promote
- Users cannot demote the last admin/super-admin
- Impersonation is blocked or tightly audited
- Session expiration behavior
- Password reset security
- Magic link security if used
- Token expiration
- Token hashing
- Single-use token behavior where required
- Rate limits on auth endpoints

## Input and API Security

Check for:

- Server-side validation
- Request body size limits
- Rate limiting
- CSRF protection where relevant
- CORS configuration
- SQL/NoSQL injection risks
- XSS risks
- Unsafe HTML rendering
- URL validation
- File path traversal
- SSRF risks from user-provided URLs
- Error messages leaking internals
- API routes exposing private data
- Mass assignment vulnerabilities
- Object-level authorization checks

## Upload Security

Check for:

- MIME allow-list
- Extension allow-list
- File signature verification
- Size limits
- Blocking executable/script file types
- Double-extension spoofing detection
- Private vs public storage separation
- Virus/malware scanning plan if needed
- Image processing safety
- PDF handling safety
- Signed URLs or restricted access for private files
- Storage cleanup jobs
- Quarantine/rejection states
- Upload audit logging

## Browser and Header Security

Check for:

- Content Security Policy
- X-Frame-Options or `frame-ancestors`
- Permissions-Policy
- Referrer-Policy
- Strict-Transport-Security
- Secure cookies
- HttpOnly cookies
- SameSite cookies
- No mixed content
- No unsafe inline scripts unless justified
- No exposed source maps in production unless intentional

## Secret Management

Check for:

- No secrets in repo
- No secrets in frontend bundle
- `.env.example` contains placeholders only
- Production secrets are env-only
- Secret rotation runbook exists
- Webhook secrets are required and verified
- API keys are scoped minimally
- Public keys are clearly marked as public-safe

Security output:

```text
Finding
Severity
Exploit Scenario
Affected File / Route
Recommended Fix
Ship Blocker? Yes/No
```

---

# 8. Privacy, Legal, and Data Handling

Check for:

- Privacy policy route/page if collecting personal data
- Terms of service route/page if public
- Cookie notice if analytics/ads/tracking are used
- Data retention expectations
- Delete/export user data path if relevant
- Consent handling for marketing emails
- Transactional vs marketing email separation
- Personal data in logs
- Sensitive data in analytics
- PII in error reports
- Regional compliance concerns, if applicable
- Children/minor data concerns, if applicable
- Medical, financial, legal, or regulated data concerns, if applicable

Do not provide legal advice. Flag areas needing legal review.

---

# 9. Testing and QA Review

Review existing tests and identify missing ones.

## Required Test Categories

Check for:

- Unit tests
- Integration tests
- API route tests
- Component tests
- End-to-end tests
- Accessibility tests
- Security tests
- Permission/role tests
- Upload tests
- Form validation tests
- Error-state tests
- Empty-state tests
- Mobile/responsive tests
- SEO metadata tests
- Build/deployment tests
- Smoke tests

## Core Flow Testing

Identify the top 3 to 5 user flows and provide tests for them.

Example:

```text
Visitor discovers public page → searches/filter results → opens detail page → submits form → receives confirmation → admin/provider sees request.
```

For each flow:

```text
Flow
Happy Path
Edge Cases
Failure Cases
Permission Cases
Mobile Cases
Accessibility Cases
Test Coverage Exists?
Recommended Test
```

## Regression Checklist

Produce a regression checklist for future releases.

---

# 10. Performance Review

Check for:

- Build size
- Bundle splitting
- Unused JavaScript
- Render-blocking resources
- Image optimization
- Lazy loading
- Font loading
- Caching headers
- CDN assumptions
- API response timing
- Database/query performance risks
- N+1 query risks
- Large client-side data loads
- Pagination
- Search/index scalability
- Mobile performance
- Core Web Vitals:
  - LCP
  - CLS
  - INP

Recommended tools:

```text
Lighthouse
PageSpeed Insights
WebPageTest
Bundle analyzer
Framework profiler
```

Output:

```text
Performance Risk
Affected Route / Component
Likely User Impact
Recommended Fix
Priority
```

---

# 11. Deployment and Operations Checklist

Check whether the project has a credible production path.

## Environment and Build

Confirm:

- Build command
- Start command
- Required Node/Python/PHP/etc. version
- Required environment variables
- Hosting target
- Database requirements
- Storage requirements
- Email provider requirements
- Payment provider requirements
- Search/index requirements
- Cron/scheduled job requirements
- Webhook requirements

## Observability

Check for:

- Error logging
- Request logging
- Audit logging
- Uptime monitoring
- Alerting
- Analytics
- Conversion tracking
- Admin activity logs
- Failed job logs
- Webhook failure logs

## Rollback

Check for:

- Deployment rollback instructions
- Database rollback/migration notes
- Feature flag rollback
- Indexing/noindex rollback
- Payment/webhook rollback
- Email sending rollback
- Maintenance mode, if relevant

## Final Smoke Test

Create a smoke test checklist:

```text
Homepage loads
Key public pages load
Login works
Signup works
Admin access works
Core form submits
Email sends
Upload works
Payment/webhook works, if applicable
Search/filter works
Error page works
404 page works
Mobile layout works
Accessibility spot check passes
SEO metadata renders
Robots/sitemap correct
No console errors
No server errors
```

---

# 12. Public Launch Gate

Produce a final recommendation:

```text
GO
GO WITH WARNINGS
NO-GO
```

Use this standard:

## GO

Only use when:

- No launch blockers
- Core flows tested
- Security basics pass
- Public SEO basics pass
- Accessibility has no severe blockers
- Deployment and rollback are clear

## GO WITH WARNINGS

Use when:

- The project can launch safely
- Some non-critical items remain
- Risks are documented and accepted

## NO-GO

Use when:

- Auth/authorization is unsafe
- Secrets are exposed
- Core flow is broken
- Build/deploy fails
- Uploads are unsafe
- Public pages are misleading
- Payment/webhook/email flows are unverified but required
- Accessibility has severe blockers
- SEO would unintentionally block indexing or expose staging content

---

# 13. Required Output Format

Return this exact structure:

```md
# Final Production Gate Review

## 1. Executive Summary

## 2. Go / No-Go Recommendation

## 3. Launch Blockers

| Priority | Area | Issue | Evidence | Required Fix |

## 4. High-Risk Warnings

| Priority | Area | Issue | Impact | Recommended Fix |

## 5. Category Audit

### Repository Hygiene
### SEO
### Accessibility
### UI / UX
### Security
### Privacy / Legal
### Testing / QA
### Performance
### Deployment / Operations

## 6. Route and API Maturity Matrix

| Route / Endpoint | Public/Admin/API | Auth Required | Server Validation | Rate Limit | Audit Log | SEO/Metadata | Status | Notes |

## 7. Core Flow Test Matrix

| Flow | Happy Path | Edge Cases | Failure Cases | Existing Coverage | Missing Tests |

## 8. Security Findings

| Severity | Finding | Exploit Scenario | Fix | Ship Blocker |

## 9. SEO Findings

| Route | Title | Meta | H1 | Canonical | Indexable | Structured Data | Issues |

## 10. Accessibility Findings

| Severity | Component / Route | Issue | WCAG Area | Fix |

## 11. Production Checklist

## 12. Accepted Risk Register

| Risk | Impact | Reason Accepted | Owner | Review Date |

## 13. Patch Plan

## 14. Final Notes
```

---

# 14. Optional Implementation Instruction

If asked to fix issues, follow this rule:

```text
Fix blockers first.
Then security warnings.
Then broken core flows.
Then accessibility blockers.
Then SEO blockers.
Then performance.
Then polish.
```

Do not introduce new features during a production gate unless they are required to safely ship.

---

# 15. Final Reminder

Your job is to protect the production launch.

Do not be optimistic by default.

Do not call mock behavior production-ready.

Do not rely on UI-only protections.

Do not ignore accessibility.

Do not ignore SEO if the project is public.

Do not ignore security because the project is “small.”

Do not bury launch blockers in a long list of minor recommendations.

Return the clearest possible go/no-go decision.
