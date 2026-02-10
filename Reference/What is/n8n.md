#workflows #dev #definition 

[https://n8n.io](https://n8n.io)

n8n (pronounced “n-eight-n”) is an open-source **workflow automation** platform designed to connect applications, APIs, and services using event-driven or scheduled workflows. It’s often described as a self-hostable, developer-friendly alternative to tools like Zapier or Make, with a strong focus on flexibility, data ownership, and extensibility.

n8n is particularly popular in DevOps, platform engineering, and data automation use cases where control, custom logic, and on-prem or Kubernetes deployment matter.

## Core concepts

- **Workflow**
    - A directed graph of nodes representing triggers, actions, and logic
    - Executed on events (webhooks), schedules (cron), or manually

- **Nodes**
    - Individual steps in a workflow
    - Can represent SaaS integrations, HTTP calls, transformations, or custom code

- **Triggers**
    - Start workflows (Webhook, Cron, GitHub events, Kafka, etc.)

- **Execution engine**
    - Runs workflows with retry logic, error handling, branching, and looping

## Key features

- **Open source**
    - Source-available with a strong OSS core
    - No hard SaaS lock-in

- **Self-hostable**
    - Runs as:
        - Single binary
        - Docker container
        - Kubernetes deployment
    - Suitable for air-gapped or regulated environments

- **Low-code + full-code**
    - Visual workflow builder
    - JavaScript code nodes for complex logic
    - Full access to input/output JSON between nodes

- **Large integration ecosystem**
    - 400+ prebuilt nodes (AWS, GitHub, Slack, Jira, Kubernetes APIs, databases, etc.)
    - Generic HTTP and GraphQL nodes for anything else

- **Stateful workflows**
    - Can persist execution state
    - Supports long-running workflows and resumability