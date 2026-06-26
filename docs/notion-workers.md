# Notion Workers Overview

Notion Workers are small Node.js and TypeScript programs that extend Notion. You write the worker code, deploy it with the Notion CLI, and Notion hosts and runs it for you, so there are no servers to manage.

## What you can build

### Sync external data

Use a sync capability to pull data from services such as Salesforce, Stripe, GitHub, or any API into Notion databases. Syncs can keep Notion databases updated automatically on a schedule.

### Add Notion AI agent tools

Use a tool capability to give Notion Custom Agents new functions they can call on demand, such as creating a Jira ticket or looking up customer information in a CRM.

### Receive webhooks

Use a webhook capability to receive HTTP events from external services such as GitHub, Stripe, Zendesk, or any service that sends webhook requests.

## How Notion Workers work

A worker is a single TypeScript file that exports a `Worker` instance from `@notionhq/workers`. You register capabilities on the worker, then deploy it with the Notion CLI.

```typescript
import { Worker } from "@notionhq/workers";

const worker = new Worker();
export default worker;

// Register capabilities on the worker.
worker.tool("sayHello", { /* ... */ });
worker.sync("customersSync", { /* ... */ });
worker.webhook("onGithubPush", { /* ... */ });
```

After deployment, Notion runs the registered capabilities:

- **Syncs** run on a schedule, defaulting to every 30 minutes, and write results to Notion databases.
- **Tools** appear in Notion Custom Agents and are called by agents when needed.
- **Webhooks** receive external HTTP events and run handlers asynchronously.

Worker code runs in a sandboxed Node.js environment. Workers can make HTTP requests to external APIs, use secrets stored through the CLI, and authenticate with third-party services through OAuth.

## Core concepts

| Concept | What it does |
| --- | --- |
| Worker | The container for your code. Each project has one worker. |
| Capability | Something the worker can do, such as a sync, tool, or webhook. |
| Database | A Notion database managed by a sync, with its schema defined in code. |
| Pacer | A rate limiter for outbound API calls so workers avoid third-party quotas. |
| OAuth | Authorization flow support for services such as GitHub and Google. |
| Secrets | Secure environment variables injected at runtime. |

## Typical workflow

1. Scaffold a worker project.

   ```bash
   ntn workers new my-worker
   cd my-worker
   ```

2. Add syncs, tools, or webhooks to `src/index.ts`.
3. Deploy the worker.

   ```bash
   ntn workers deploy
   ```

4. Iterate by editing code and redeploying.

## Where Workers fit into Notion

Notion Workers are the hosted execution layer for extending Notion with code. They connect external systems, Notion databases, and Notion AI Custom Agents without requiring a separate server. This makes them useful for scheduled data synchronization, agent-accessible business actions, and event-driven integrations.
