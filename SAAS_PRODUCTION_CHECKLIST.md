# SaaS Production Checklist

This repository is currently a powerful design platform, but shipping a multi-tenant SaaS requires product-specific integrations. This checklist defines the required hardening and integration work.

## 1) Authentication and user accounts

- Add server-side auth middleware to all mutating API routes.
- Require verified sessions for document create/read/update/delete.
- Implement user profile model: `id`, `email`, `name`, `avatar_url`, `created_at`, `updated_at`.
- Add sign-in, sign-up, sign-out, password reset, email verification UX.

## 2) Multi-tenant private workspaces

- Introduce `workspace` and `project` ownership fields.
- Add `owner_user_id` (and optional `workspace_id`) to persisted document tables.
- Enforce tenant filtering in every data read path.
- Add database-level Row Level Security (RLS) policies as the primary isolation control.

## 3) Plan and billing system

Plans:
- Free
- Pro
- Enterprise

Required entities:
- `subscription`
- `plan`
- `usage_meter`
- `invoice_ref`

Required flows:
- Checkout session creation
- Webhook verification and subscription state synchronization
- Plan-based feature gating and rate limiting

## 4) Branding and white-labeling

- Replace default app name and metadata title/description.
- Replace favicon and social images.
- Replace landing and UI copy that references the upstream project name.
- Centralize branding in one config module to avoid hardcoded strings.

## 5) AI provider production setup

- Store API keys in production secret manager (not git, not plain env files on dev laptops).
- Support provider failover order and feature flags.
- Add per-plan AI quotas and per-user rate limits.
- Add structured logging with secret redaction.

## 6) Operational hardening

- Add production DB migrations and rollback strategy.
- Add health checks for web, database, and AI provider adapters.
- Add error monitoring (Sentry or equivalent).
- Add audit log for account, billing, and project mutations.
- Ensure backups and restore drill for user projects.

## 7) CI/CD quality gates

- Type check
- Unit/integration tests
- Build verification
- Security scan (dependencies + secrets)
- Migration checks

## 8) Launch acceptance criteria

- User A cannot view User B projects by API, web, or desktop flows.
- Free/Pro/Enterprise limits are enforced server-side.
- Billing events are idempotent.
- All secrets are externalized and rotated.
- Incident runbook exists.
