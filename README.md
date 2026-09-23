# Cloudflare Production Architecture Skill

## Purpose

এই skill-এর উদ্দেশ্য হলো Next.js ভিত্তিক production application-কে Cloudflare-first, serverless এবং maintainable architecture-এ build, configure, deploy এবং verify করা।

Default deployment target হিসেবে Cloudflare ecosystem বিবেচনা করতে হবে, যদি project-এর technical requirements Cloudflare Workers runtime-এর সঙ্গে compatible হয়।

Primary architecture:

GitHub
↓
Cloudflare Workers Builds / CI
↓
Cloudflare Workers
├── D1 → Structured application data
└── R2 → Files and media

Cloudflare-এর CDN, DNS, TLS, security এবং edge capabilities প্রয়োজন অনুযায়ী ব্যবহার করতে হবে।

---

## Core Technology Stack

Preferred stack:

* Next.js
* React
* TypeScript
* Tailwind CSS
* Cloudflare Workers
* Cloudflare D1
* Cloudflare R2
* GitHub
* GitHub Actions where CI/CD automation is useful
* vinext or the currently recommended Cloudflare-compatible Next.js deployment approach

Do not introduce unnecessary infrastructure such as:

* VPS
* Ubuntu server
* Nginx
* PM2
* PHP-FPM
* manually managed Node.js server
* manually managed database server
* local production file storage

unless the project's requirements explicitly justify them.

---

## Architecture Decision Rule

Before implementation, inspect the project requirements.

Determine:

1. Application type
2. Runtime requirements
3. Database requirements
4. File/media requirements
5. Authentication requirements
6. Background job requirements
7. Cron/scheduled task requirements
8. External API requirements
9. WebSocket/real-time requirements
10. Expected traffic
11. CPU/memory requirements
12. Node.js compatibility requirements
13. Cloudflare Workers compatibility
14. Expected cost and free-tier suitability

Cloudflare Workers should be the default target only when the application is technically compatible.

Never force Cloudflare into a project where it creates unnecessary architectural problems.

---

## Application Layer

Use Next.js as the primary application framework.

Prefer:

* App Router
* Server Components where appropriate
* Server Actions where appropriate
* Route Handlers for API endpoints
* SSR when dynamic server-rendered content is required
* SSG/static generation where appropriate
* Incremental/static strategies where supported
* TypeScript throughout the application

Keep browser-only code isolated from server-side code.

Avoid unnecessary client-side rendering.

---

## Cloudflare Workers

Workers are the application runtime.

The deployment architecture must avoid unnecessary traditional server infrastructure.

The agent must verify:

* Next.js compatibility
* runtime compatibility
* supported Node.js APIs
* package compatibility
* environment variable handling
* bindings
* build configuration
* production runtime behavior

Use the current Cloudflare-recommended deployment tooling.

If vinext is appropriate for the project, configure it correctly.

Do not assume that every Next.js package or Node.js dependency works inside Workers.

Audit dependencies before deployment.

---

## Database: Cloudflare D1

Use D1 for structured relational application data when appropriate.

Examples:

* users
* posts
* categories
* tags
* comments
* products
* orders
* settings
* metadata
* application configuration

Design the schema before implementation.

Use:

* normalized relational structure where appropriate
* primary keys
* foreign keys where supported and useful
* indexes
* unique constraints
* timestamps
* migrations

Never store large binary files inside D1.

Keep database queries efficient.

Avoid unnecessary queries from server-rendered pages.

Use prepared/parameterized queries.

Validate all external input before database operations.

---

## File Storage: Cloudflare R2

Use R2 for object/file storage.

Examples:

* images
* videos
* PDFs
* documents
* user uploads
* CMS media
* downloadable assets

Do not store large media files in D1.

Store the object in R2 and keep only the required metadata/reference in D1.

For uploads, implement:

* authentication
* authorization
* MIME/type validation
* extension validation
* file-size limits
* filename sanitization
* safe object keys
* appropriate caching
* deletion handling
* orphan-file cleanup where appropriate

Never trust client-provided filenames or MIME types.

---

## Authentication and Authorization

Production applications must separate:

Authentication:

"Who is this user?"

from authorization:

"What is this user allowed to do?"

For admin/CMS systems:

* protect admin routes
* protect server actions
* protect API endpoints
* validate sessions server-side
* enforce role-based permissions
* never rely only on UI restrictions

Never expose privileged D1 or R2 operations directly to untrusted clients.

---

## Security

Treat security as an application requirement, not merely a Cloudflare feature.

Implement appropriate protection against:

* XSS
* SQL injection
* CSRF
* broken authorization
* insecure file uploads
* credential leakage
* secret exposure
* session abuse
* brute-force attempts
* excessive request rates

Use environment secrets properly.

Never commit:

* API keys
* tokens
* passwords
* private credentials
* service credentials

to Git.

Before deployment, scan the repository for accidentally exposed secrets.

---

## Environment Variables

Separate environments where practical:

* local development
* preview/staging
* production

Never hard-code secrets.

Document required environment variables.

Distinguish between:

* public variables
* server-only secrets
* Cloudflare bindings

Never expose server-only secrets through client-side code.

---

## Caching and Performance

Use Cloudflare's edge capabilities intelligently.

Optimize:

* static assets
* images
* fonts
* API responses
* database queries
* server rendering
* cache headers
* browser caching

Avoid caching private or user-specific responses publicly.

Do not blindly cache dynamic authenticated content.

Use appropriate cache invalidation strategies.

---

## SEO for Public Websites

For public-facing content applications, implement where relevant:

* metadata
* canonical URLs
* sitemap.xml
* robots.txt
* Open Graph
* Twitter/X metadata
* structured data
* semantic HTML
* clean URL architecture
* proper 404 handling
* redirects
* pagination
* internal linking
* indexability controls

For content-heavy sites, ensure important content is available in server-rendered HTML where appropriate.

---

## Observability

Production applications should have sufficient visibility into:

* application errors
* failed requests
* database errors
* deployment failures
* authentication failures
* upload failures
* performance problems

Use Cloudflare-native observability where appropriate.

Do not introduce expensive monitoring infrastructure without need.

---

## CI/CD

Preferred workflow:

Developer
↓
Git
↓
GitHub
↓
Validation
↓
Build
↓
Cloudflare deployment
↓
Production verification

Before deployment, run where applicable:

* lint
* typecheck
* unit tests
* integration tests
* production build

Never deploy knowingly broken code.

---

## Deployment Verification

After every production deployment verify:

1. Application loads
2. Homepage works
3. Critical routes work
4. Authentication works
5. Database operations work
6. R2 uploads work
7. R2 retrieval works
8. API endpoints work
9. Environment variables are available
10. No obvious runtime errors exist
11. SEO-critical pages work
12. robots.txt works
13. sitemap.xml works
14. HTTPS works
15. Cloudflare bindings work
16. Production build is reproducible

If the application has a CMS, additionally test:

* login
* create
* edit
* delete
* publish
* media upload
* media deletion
* category management
* tag management
* permissions

---

## Cost Awareness

Cloudflare's free tiers and paid limits can change.

Never claim that a project will remain permanently free.

Before production deployment:

* inspect current pricing
* identify applicable free-tier limits
* estimate expected usage
* identify potential cost drivers
* document where costs could increase

Important cost dimensions may include:

* Worker requests
* compute
* D1 storage/operations
* R2 storage
* R2 operations
* bandwidth/egress depending on service
* build/deployment usage

Use the smallest architecture that satisfies the actual requirement.

---

## Infrastructure as Code / Configuration

Keep deployment configuration inside the repository whenever practical.

Configuration should be version-controlled.

Document:

* Worker configuration
* D1 database
* migrations
* R2 buckets
* environment variables
* deployment commands
* local development setup
* production setup

Do not depend on undocumented manual configuration.

---

## Development Workflow

For every new project:

1. Inspect requirements.
2. Identify Cloudflare compatibility.
3. Design architecture.
4. Initialize Next.js.
5. Configure TypeScript.
6. Configure Tailwind.
7. Configure Cloudflare runtime.
8. Configure D1 if required.
9. Configure R2 if required.
10. Implement application.
11. Implement security.
12. Implement tests.
13. Configure CI/CD.
14. Deploy to preview/staging.
15. Verify.
16. Deploy production.
17. Verify production.
18. Document the architecture.

---

## Failure Handling

If Cloudflare compatibility problems appear:

1. Identify the exact incompatibility.
2. Determine whether an alternative library exists.
3. Prefer Workers-compatible libraries.
4. Avoid unnecessary Node.js-only dependencies.
5. Consider Web APIs where possible.
6. Only introduce another infrastructure component if technically justified.

Never silently replace the intended architecture.

---

## Final Principle

The objective is not:

"Use Cloudflare everywhere."

The objective is:

"Use Cloudflare as the default production platform when it provides a technically sound, maintainable and cost-effective architecture for the project."

Architecture decisions must be based on requirements rather than trend or vendor loyalty.
