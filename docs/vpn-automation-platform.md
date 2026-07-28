# VPN Automation Platform — Global Infrastructure Control Plane

## Problem

Managing dozens of VPN nodes by hand leads to configuration drift, users falling out of sync between servers, and a scaling ceiling — every new server is manual work and every mistake is a support ticket.

## Solution

A control plane that automates the entire lifecycle of a distributed VPN infrastructure: client provisioning and sync, subscription management, server health monitoring, load balancing, and protocol-level routing — administered through a Telegram bot rather than a bespoke admin panel.

## How it's implemented

**A single source of truth for clients, replicated outward.** Client and subscription state lives centrally; each VPN node is treated as a replica that gets synced, not as its own authority. This is what avoids the classic multi-server VPN problem of a user existing on server A but not server B.

**Protocol layer is deliberately modern and layered.** The platform speaks Xray Reality (TLS-camouflaged, resistant to active-probing detection), VLESS, and Hysteria2 — chosen specifically because they behave differently under DPI (deep packet inspection), so the platform can route a client to whichever protocol currently performs best in their network environment rather than betting on one.

**HAProxy sits in front as the routing and balancing layer**, so a client connection is directed to healthy backend nodes without the client needing to know server topology at all — new nodes can be added or drained without touching client configuration.

**Health monitoring is continuous and automated**, not something an operator checks manually — `systemd` timers run periodic checks against each node and feed results back into the routing decisions, so a degraded server stops receiving new connections before it fails outright.

**Administration happens entirely through a Telegram bot.** No web admin panel to build or secure separately — server provisioning, subscription changes, and monitoring alerts all flow through a chat interface that's already authenticated by Telegram itself.

**Everything runs on plain Linux servers with `systemd`**, deliberately avoiding heavier orchestration — at this scale, a fleet of well-monitored systemd services is simpler to reason about and debug than a container orchestrator would be.

## AI usage in building it

- Diagnosing network issues, especially DPI/Reality-specific failure modes that are hard to reproduce locally.
- Researching and comparing protocol configurations (Xray Reality vs. VLESS vs. Hysteria2 trade-offs) before committing to the stack.
- General infrastructure optimization — tuning timers, balancing strategy, and failure thresholds.

## Stack

Python · Xray-core · HAProxy · Linux
