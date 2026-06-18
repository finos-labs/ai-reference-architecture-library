# Hybrid Hierarchical and Parallel Multi-Agent Reference Architecture

# Executive Summary

This reference architecture defines a reusable multi-agent collaboration pattern for **hybrid hierarchical and parallel agentic workflows**.

It extends the existing multi-agent reference architecture by specialising the **Agent Collaboration Patterns** section for workflows where a supervisor agent coordinates multiple specialist worker agents, while allowing independent or semi-independent subtasks to execute in parallel.

The pattern is intended for complex enterprise workflows that require task decomposition, specialist reasoning, controlled tool usage, evidence aggregation, policy enforcement, human review, and end-to-end observability.

The architecture preserves the same layers and core components as the multi-agent reference architecture:

- User Interaction Layer
- Agent Gateway Layer
- Agent Layer
- Knowledge Layer
- LLM Layer
- MCP Layer
- Evaluation Layer
- Observability Layer

The primary architectural distinction is in the **Agent Layer**, where the workflow combines:

- **Hierarchical orchestration** through a supervisor / orchestrator agent.
- **Parallel execution** through multiple specialist worker agents.
- **Aggregation and reconciliation** of worker outputs.
- **Policy and decision gates** before action, escalation, or completion.
- **Human supervision** for high-risk, uncertain, or policy-sensitive outcomes.

# Table of Contents

- [Executive Summary](#executive-summary)
- [Architecture Overview](#architecture-overview)
- [Pattern Intent](#pattern-intent)
- [User Interaction Layer](#user-interaction-layer)
- [Agent Gateway Layer](#agent-gateway-layer)
- [Agent Layer](#agent-layer)
- [Knowledge Layer](#knowledge-layer)
- [LLM Layer](#llm-layer)
- [MCP Layer](#mcp-layer)
- [Evaluation Layer](#evaluation-layer)
- [Observability Layer](#observability-layer)
- [Reference Flow](#reference-flow)
- [Failure and Escalation Behaviour](#failure-and-escalation-behaviour)
- [Relationship to CALM](#relationship-to-calm)

# Architecture Overview

<!-- Diagram to be added after the architecture is agreed and codified in CALM. -->

This architecture describes a reusable pattern for agentic systems where no single agent should own all reasoning, execution, and decisioning responsibilities.

Instead, a supervisor agent decomposes the task, coordinates a group of specialist worker agents, reconciles their outputs, and applies policy gates before the workflow proceeds.

The pattern is hybrid because it combines two execution models:

- **Hierarchical**: the supervisor agent owns task planning, state management, delegation, coordination, aggregation, and escalation.
- **Parallel**: worker agents execute delegated subtasks concurrently where dependencies allow.

# Pattern Intent

The hybrid hierarchical and parallel pattern should be used when:

- The task can be decomposed into multiple specialist subtasks.
- Some subtasks can run concurrently.
- The workflow requires a central point of coordination and accountability.
- Multiple agents require access to different tools, models, or knowledge sources.
- Worker outputs must be reconciled before a decision or action is taken.
- The workflow requires deterministic controls, policy checks, or human review.
- End-to-end auditability and traceability are required.

This pattern should not be used when a simple sequential workflow or single-agent design is sufficient.

# User Interaction Layer

The User Interaction Layer serves as the entry point for the workflow.

## User

Represents the individual or system initiating a request. In this pattern, the user may submit a task, provide additional information, review intermediate results, or approve a final action.

The user does not interact directly with specialist worker agents. User interaction is mediated through the application and governed through the Agent Gateway Layer.

## Application

Provides the user-facing or system-facing interface for initiating and managing the workflow.

The application submits the task to the Agent Gateway, receives workflow status updates, and presents outputs, requests for clarification, or human review actions back to the user.

In human-in-the-loop scenarios, the application provides the review surface where a human can approve, reject, amend, or escalate the supervisor agent's recommendation.

# Agent Gateway Layer

The Agent Gateway Layer provides the controlled entry point into the agentic workflow.

## Agent Registry

The Agent Registry maintains metadata about the supervisor agent and the available worker agents.

In this pattern, registry metadata should include agent capabilities, version, owner, approved tools, allowed data domains, execution constraints, and escalation behaviour.

The supervisor agent uses this information to identify which worker agents are eligible for delegated subtasks.

## Gateway

The Gateway validates incoming requests and routes them to the supervisor agent.

It ensures that the workflow starts from the approved supervisory entry point rather than allowing applications to invoke specialist worker agents directly.

This preserves hierarchy, centralised state management, policy enforcement, and auditability.

## Guardrails and Policies

Guardrails and policies apply before the task reaches the supervisor agent.

Controls include authentication, authorisation, input validation, content filtering, request classification, rate limiting, and policy enforcement.

For this pattern, guardrails should also define which classes of workflow can run automatically and which require human review before action.

# Agent Layer

The Agent Layer is the core of the hybrid hierarchical and parallel pattern.

## Agent Collaboration Patterns

This reference architecture specialises the existing multi-agent collaboration model with a hybrid pattern composed of:

- **Supervisor/Worker**: A supervisor agent decomposes the task and coordinates specialist worker agents.
- **Parallel Worker Execution**: Multiple worker agents execute independent or semi-independent subtasks concurrently where dependencies permit.
- **Aggregation and Reconciliation**: Worker outputs are consolidated, compared, ranked, reconciled, or rejected before use.
- **Policy and Decision Gate**: Consolidated outputs are checked against deterministic rules, risk thresholds, and escalation criteria.
- **Human Review / Escalation**: Uncertain, conflicting, high-risk, or policy-sensitive outputs are routed to human supervision.

The supervisor agent is responsible for:

- Understanding the task objective.
- Creating the execution plan.
- Determining task dependencies.
- Selecting worker agents.
- Delegating subtasks.
- Managing workflow state.
- Monitoring worker progress.
- Aggregating worker outputs.
- Resolving conflicts.
- Applying decision criteria.
- Routing to automated action, human review, or escalation.

Worker agents are responsible for:

- Executing bounded specialist subtasks.
- Using only approved tools and knowledge sources.
- Returning structured outputs.
- Providing confidence, evidence, or rationale where required.
- Reporting errors, uncertainty, or policy constraints back to the supervisor.

## Unified Agent Runtime

The Unified Agent Runtime provides the secure execution environment for both the supervisor agent and worker agents.

- **State Management**: Maintains workflow state across the supervisor and worker agents, including task status, dependencies, intermediate outputs, confidence scores, and escalation decisions.
- **Secure Execution**: Ensures that agent execution, tool calls, credentials, and workspace access are isolated and governed.
- **Collaboration/Handoff**: Enables the supervisor to delegate subtasks to worker agents and receive structured results.
- **Adaptive Learning**: Captures learning signals from execution outcomes, human reviews, failed tasks, and policy interventions.
- **Workspace File System**: Provides a controlled workspace for shared artifacts, intermediate files, evidence packages, and generated outputs.

- **Tools Layer**:
    - **MCP Client**: Provides governed access to external MCP servers through the MCP Layer.
    - **Shell Tool**: Supports sandboxed command execution where permitted.
    - **I/O Tool**: Supports reading and writing workflow artifacts in the workspace.
    - **Web Search Tool**: Supports governed web retrieval where allowed by policy.

- **Short-Term Memory**:
    - **In-session context manager**: Maintains the immediate task context, execution plan, worker responses, and reconciliation state.

- **Long-Term Memory**:
    - **Session summaries**: Stores durable summaries of completed workflows where permitted.
    - **User/Task personalization**: Retains approved preferences or task-specific configuration where permitted.

# Knowledge Layer

The Knowledge Layer provides grounding data and retrieval services for the supervisor and worker agents.

## Source Bases

Source Bases provide authoritative structured and unstructured information required by the workflow.

In this pattern, each worker agent should access only the source bases required for its delegated task.

## Vector DBs

Vector DBs provide semantic retrieval and grounding over approved corpora.

Retrieval results should be traceable so that the supervisor agent can include evidence and provenance in the aggregated output.

# LLM Layer

The LLM Layer provides governed access to approved large language models.

## Model Registry

The Model Registry maintains approved models, versions, capabilities, constraints, and usage policies.

Different worker agents may use different approved models depending on task requirements.

## LLM Gateway

The LLM Gateway routes model requests from the supervisor and worker agents to approved model endpoints.

It centralises model access, policy enforcement, monitoring, and logging.

## Guardrails and Policies

LLM guardrails apply input validation, prompt-injection protection, output filtering, data loss prevention, model usage limits, and policy checks.

In this pattern, guardrails should apply independently to the supervisor and each worker agent, because each agent may receive different context and produce different outputs.

# MCP Layer

The MCP Layer provides governed access to external tools, APIs, and enterprise systems.

## MCP Server Registry

The MCP Server Registry maintains approved MCP servers, capabilities, owners, usage constraints, and access policies.

## MCP Gateway

The MCP Gateway brokers access between the agent runtime and MCP servers.

It ensures that worker agents can only access the tools and services assigned to their role.

## Guardrails and Policies

MCP guardrails enforce authentication, authorisation, parameter validation, tool allow-lists, rate limits, and execution constraints.

In this pattern, tool permissions should be scoped per agent role and per workflow state.

# Evaluation Layer

The Evaluation Layer provides oversight and quality assurance for the workflow.

## Feedback Engine

The Feedback Engine collects feedback from users, reviewers, automated tests, runtime checks, and downstream outcomes.

Feedback should be associated with the supervisor decision, worker outputs, policy gate outcomes, and human review actions.

## Human Supervision

Human Supervision provides mechanisms for reviewing, approving, correcting, or rejecting outputs.

In this pattern, human supervision is triggered when the policy and decision gate detects uncertainty, conflict, high impact, low confidence, or policy sensitivity.

## Runtime Protection

Runtime Protection monitors the supervisor and worker agents during execution.

It may block unsafe actions, stop tool calls, require additional validation, or escalate the workflow to a human reviewer.

# Observability Layer

The Observability Layer provides end-to-end visibility across the full hybrid workflow.

## Logs

Logs capture requests, supervisor plans, worker task assignments, tool calls, model calls, policy decisions, and human review actions.

## Traces

Traces link the full request lifecycle across the application, gateway, supervisor agent, worker agents, LLM gateway, MCP gateway, knowledge retrieval, evaluation, and observability services.

## Metrics

Metrics include latency, throughput, worker execution duration, model usage, tool usage, escalation rates, error rates, policy violations, and human review outcomes.

## Events

Events capture significant workflow transitions, including task decomposition, worker assignment, worker completion, aggregation, policy gate decisions, escalation, and final outcome.

## Anomaly Detection

Anomaly Detection identifies unusual behaviour across agents, tools, models, and workflow outcomes.

## Correlation Engine

The Correlation Engine links logs, traces, metrics, and events to provide a unified view of the workflow.

# Reference Flow

1. A user or application submits a task.
2. The Agent Gateway validates the request and routes it to the supervisor agent.
3. The supervisor agent decomposes the task into subtasks.
4. The supervisor identifies which subtasks can run in parallel.
5. The supervisor selects worker agents from the Agent Registry.
6. Worker agents execute delegated subtasks through the Unified Agent Runtime.
7. Worker agents access models, tools, and knowledge through governed gateways.
8. Worker agents return structured outputs, confidence, evidence, and errors.
9. The supervisor aggregates and reconciles worker outputs.
10. The policy and decision gate evaluates the consolidated result.
11. The workflow proceeds to automated action, human review, or escalation.
12. Evaluation and observability layers capture evidence across the workflow.

# Failure and Escalation Behaviour

The pattern should support explicit failure and escalation behaviour.

Escalation may occur when:

- Worker outputs conflict.
- Required evidence is missing.
- Confidence is below threshold.
- A tool call fails.
- A policy violation is detected.
- A high-impact decision is requested.
- A human approval requirement is triggered.

Failure handling should preserve workflow state, evidence, intermediate outputs, and audit records.

# Relationship to CALM

This architecture is intended to be codified in CALM after the pattern-level structure is agreed.

The CALM representation should capture:

- The eight reference architecture layers.
- The supervisor agent and worker agent group.
- Agent-to-agent relationships.
- Parallel execution relationships.
- Gateway and guardrail boundaries.
- Knowledge, LLM, and MCP dependencies.
- Evaluation and human supervision paths.
- Observability relationships.
- Policy and decision gate relationships.
