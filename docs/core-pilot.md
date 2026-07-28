# Core-Pilot — AI-powered Repair Center ERP SaaS

## Problem

Most repair centers run on Excel, Telegram, and disconnected CRMs — there is no single flow from device intake to handoff. Every workshop reinvents its own process, and owners have no visibility across locations.

## Solution

A multi-tenant SaaS platform that covers the full repair-center workflow: order creation, receipts, warranty tracking, repair checklists, device password locking, staff roles, and billing — usable by a single workshop or a chain of them from one account.

## How it's implemented

**Multi-tenancy is the foundation, not an afterthought.** Every table that holds business data carries a tenant identifier, and every query is scoped to it at the data-access layer — not left to individual endpoints to remember. This is what makes it safe to run dozens of independent repair businesses on one deployment.

**Identifiers are UUID v7.** Time-sortable UUIDs let primary keys double as a natural creation-order index, which matters a lot for a system where "show me the latest orders" is the single most common query.

**Access control is RBAC, modeled explicitly.** Roles (owner, manager, technician, etc.) are first-class entities with their own permission sets, rather than a handful of boolean flags scattered across the user table. New roles or permission combinations don't require code changes.

**State changes go through an event-driven core.** Actions like "order status changed" or "device handed over" are emitted as domain events rather than triggering side effects inline. Notifications, billing updates, and audit trails all subscribe to the same event stream instead of being hard-wired into the action that caused them.

**Writes and side effects are kept consistent with the Outbox pattern.** When an action needs to both change data and trigger something external (a Telegram notification, a webhook), the outbound message is written to the database in the same transaction as the state change, then dispatched asynchronously. This avoids the classic failure mode where the database commits but the notification is lost — or the reverse.

**Feature flags gate rollout.** New modules (billing changes, new checklist types) ship dark and get turned on per-tenant, so a chain of repair centers can be migrated gradually instead of all-or-nothing.

**Billing is modeled as its own subsystem**, decoupled from the operational data — subscriptions, usage, and invoicing don't leak into the domain logic that handles repairs.

## AI usage in building it

- Architecture design — working through the multi-tenant data model and the event/outbox interaction with an LLM before writing implementation code.
- Code generation and review for the backend modules.
- Systematic edge-case discovery (concurrent status changes, tenant boundary violations, partial failures in the outbox dispatcher).
- Security auditing of the permission model.
- Refactoring legacy sections as the schema evolved.

## Stack

NestJS · TypeScript · PostgreSQL · Prisma
