# AVT-Manage — AI bot for Avito lead capture

## Problem

Businesses lose Avito leads to slow response times. By the time a manager sees a message, the client has already messaged a competitor — especially at night or on weekends.

## Solution

An AI bot that answers Avito messages within seconds, extracts the details needed to qualify a lead (device, issue, contact), creates the lead automatically in a CRM, and notifies the assigned manager on Telegram in real time — with a night mode that keeps capturing leads even when no one is online to reply live.

## How it's implemented

**The bot is the first responder, not a chatbot gimmick.** It doesn't just acknowledge messages — it runs a structured extraction step against the conversation to pull out the fields a lead actually needs (what device, what's wrong, how to reach the client), and only creates the CRM record once it has enough to be useful to a manager.

**Every bot decision is logged, not just the final action.** What it recognized, why it responded the way it did, whether it created a lead — all visible after the fact. This turns "the bot did something weird" from a mystery into something a manager can actually inspect and trust.

**Night mode decouples response from capture.** Outside working hours the bot stops sending live replies but keeps creating leads in the background, so the team sees a queue of overnight inquiries each morning instead of losing them entirely.

**Two purpose-built Telegram bots, not one do-everything bot.** A sales bot handles the customer-facing conversation and lead qualification; a separate delivery bot handles the manager-facing side — notifications, lead handoff, status updates. Splitting them keeps each bot's conversation logic simple and testable in isolation.

**Scheduled jobs handle everything that isn't a live event** — subscription renewal checks, stale-lead follow-ups, and periodic sync tasks run on cron-style schedules rather than being bolted onto request handlers.

**The client dashboard is a separate app from the bot backend.** Managers get a React/Vite panel to see leads, statuses, and conversation history — built and deployed independently from the bot service it talks to, so either side can change without redeploying the other.

**Roles and multi-manager support are built in from the data model up** — a manager sees only their assigned leads, an owner sees everything, and tier limits (manager count, monthly conversation volume) are enforced the same way subscription tiers are.

## AI usage in building it

- Designing the bot's business logic and conversation/extraction flow — what counts as "enough information to create a lead," and how to handle ambiguous replies.
- Designing the database schema and Prisma models around leads, conversations, and subscriptions.
- Debugging the Avito API integration, including its quirks around message polling and rate limits.
- Writing and reviewing the test suite.

## Stack

Node.js · Express · TypeScript · Prisma · PostgreSQL · Telegram Bot API · React · Vite
