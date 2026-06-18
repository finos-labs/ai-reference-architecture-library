# Loan Approval Multi-Agent Solution Architecture

# Executive Summary

This solution architecture instantiates the Hybrid Hierarchical and Parallel Multi-Agent Reference Architecture for an AI-enhanced loan approval workflow.

The solution uses a workflow engine as the workflow orchestrator for deterministic workflow management and AI-enabled task execution. The workflow engine manages process state, routing, parallel branch execution, retries, human tasks, auditability, and task-level telemetry.

In this solution, specialist agent responsibilities are implemented as workflow-engine-managed AI-enabled tasks. Each AI-enabled task can prompt approved models and use a dedicated list of tools or subtasks. This allows the workflow to combine deterministic orchestration with governed agentic execution.

Task, model, and tool consumption are monitored through the Observability Layer. Observability dashboards show task performance, model usage, tool usage, latency, errors, and consumption.

# Table of Contents

- [Executive Summary](#executive-summary)
- [Architecture Overview](#architecture-overview)
- [Relationship to the Reference Architecture](#relationship-to-the-reference-architecture)
- [User Interaction Layer](#user-interaction-layer)
- [Agent Gateway Layer](#agent-gateway-layer)
- [Agent Layer](#agent-layer)
- [Knowledge Layer](#knowledge-layer)
- [LLM Layer](#llm-layer)
- [MCP Layer](#mcp-layer)
- [Evaluation Layer](#evaluation-layer)
- [Observability Layer](#observability-layer)
- [Loan Approval Reference Flow](#loan-approval-reference-flow)
- [AIGF Control Mapping](#aigf-control-mapping)
- [Relationship to CALM](#relationship-to-calm)

# Architecture Overview

<!-- Diagram to be added after the solution architecture is agreed and codified in CALM. -->

# Relationship to the Reference Architecture

This solution architecture is derived from the Hybrid Hierarchical and Parallel Multi-Agent Reference Architecture.

The reference architecture defines the reusable pattern:

- Hierarchical orchestration
- Parallel specialist-agent execution
- Aggregation and reconciliation
- Policy and decision gates
- Human review and escalation
- Runtime, evaluation, and observability controls

This solution instantiates that pattern for loan approval.

The workflow engine implements the orchestration responsibilities of the supervisor pattern. It coordinates deterministic workflow tasks, AI-enabled tasks, parallel branches, decision gates, human review steps, and downstream integrations.

The specialist worker-agent responsibilities are implemented as AI-enabled workflow tasks. Each AI-enabled workflow task is configured with a task purpose, model or model-routing configuration, prompt or task instruction, dedicated tool allow-list, input/output contract, policy controls, and telemetry.

# User Interaction Layer

The User Interaction Layer provides the channels through which applicants, loan officers, and operational users interact with the loan approval workflow.

## Applicant

The applicant submits a loan application and supporting documents such as PDFs, images, identity evidence, income documents, bank statements, or other financial records.

The applicant receives requests for additional information, loan agreement documents, e-signature requests, and final status updates.

## Loan Officer

The loan officer reviews applications that require human judgment.

Manual review may be triggered by medium risk, low model confidence, fraud indicators, policy exceptions, missing evidence, or escalation criteria.

## Loan Origination Application

The loan origination application provides the user-facing interface for application intake, document upload, status tracking, human review, and decision presentation.

It submits workflow requests into the Agent Gateway Layer and receives status updates and task outcomes from the workflow engine.

# Agent Gateway Layer

The Agent Gateway Layer provides the controlled entry point into the agentic loan approval workflow.

## Agent Registry

The Agent Registry maintains metadata about AI-enabled workflow tasks and their associated capabilities.

For this solution, registry metadata should include:

- AI task name
- Task purpose
- Model configuration
- Prompt or instruction version
- Tool allow-list
- Data access boundaries
- Owner
- Runtime constraints
- Escalation behaviour
- Telemetry requirements

## Gateway

The Gateway validates incoming workflow requests and routes approved requests into the workflow engine.

It ensures that applications initiate loan approval workflows through a governed entry point rather than invoking AI tasks, tools, or models directly.

## Guardrails and Policies

Guardrails and policies apply before the workflow starts and throughout task execution.

Controls include authentication, authorization, input validation, document checks, data classification, prompt-injection checks, policy enforcement, and routing rules for human review.

# Agent Layer

The Agent Layer implements the hybrid hierarchical and parallel multi-agent solution.

## Agent Collaboration Patterns

The solution uses the Hybrid Hierarchical and Parallel pattern.

The workflow engine acts as the workflow orchestrator and implements the supervisor responsibilities:

- Own workflow state
- Route tasks
- Coordinate deterministic and AI-enabled tasks
- Execute parallel branches
- Manage retries and timeouts
- Enforce task ordering and dependencies
- Invoke decision gates
- Route to human review
- Emit task, model, and tool telemetry

Specialist worker-agent responsibilities are implemented as AI-enabled workflow tasks:

- Document Intelligence Task
- Fraud Detection Task
- Compliance Review Task
- Credit Risk Assessment Task
- Dynamic Pricing Task
- Document Generation Task

Where task dependencies permit, the workflow engine can execute AI-enabled workflow tasks in parallel. For example, document validation, fraud checks, and credit risk preparation may run as coordinated branches once required application data is available.

## Unified Agent Runtime

The Unified Agent Runtime provides the execution environment for workflow-engine-managed AI-enabled tasks.

### State Management

The workflow engine manages process state, task state, branch state, retry state, human review state, and final outcome state.

### Secure Execution

AI-enabled workflow tasks execute with scoped permissions, approved model access, and dedicated tool allow-lists.

### Collaboration / Handoff

The workflow engine coordinates handoff between workflow steps, AI-enabled tasks, deterministic checks, human review, document generation, e-signature, and disbursement.

### Adaptive Learning

Feedback from human review, task outcomes, fraud findings, credit decisions, and downstream performance can be captured for future evaluation and model improvement, subject to governance approval.

### Workspace File System

The workflow may maintain a controlled workspace for application documents, extracted fields, validation results, model outputs, generated agreements, and audit evidence.

### Tools Layer

Each AI-enabled workflow task is bound to a dedicated set of tools or subtasks.

Example tool bindings:

- Document Intelligence Task: OCR, document parser, field validation service
- Fraud Detection Task: identity verification, fraud rules, anomaly scoring
- Compliance Review Task: KYC checks, sanctions screening, evidence retrieval
- Credit Risk Assessment Task: credit bureau API, risk model, policy thresholds
- Dynamic Pricing Task: pricing model, market data, risk-adjusted pricing rules
- Document Generation Task: template engine, PDF generation, e-signature integration

### Short-Term Memory

Short-term memory holds workflow-local context, task inputs, intermediate outputs, branch results, evidence, and decision context.

### Long-Term Memory

Long-term memory should be limited to governed summaries, approved operational metrics, and permitted feedback records. Sensitive applicant data should not be retained outside approved systems of record.

# Knowledge Layer

The Knowledge Layer provides governed access to approved data and evidence sources.

## Source Bases

Source bases may include:

- Loan application records
- Uploaded applicant documents
- Product policy documents
- Underwriting policy
- Fraud typologies
- Compliance rules
- Pricing policy
- Audit evidence

## Vector DBs

Vector databases may support retrieval over approved policy documents, product terms, operating procedures, and compliance guidance.

Retrieved content should be traceable and available for audit and human review.

# LLM Layer

The LLM Layer provides governed access to approved models used by AI-enabled workflow tasks.

## Model Registry

The Model Registry maintains approved models, model versions, allowed use cases, risk classifications, owners, and constraints.

Different AI-enabled workflow tasks may use different model configurations depending on task purpose and risk.

## LLM Gateway

The LLM Gateway routes model requests from AI-enabled workflow tasks to approved models.

It centralizes model access, monitoring, logging, and policy enforcement.

## Guardrails and Policies

LLM guardrails apply prompt validation, output validation, data loss prevention, prompt-injection mitigation, model usage limits, and task-specific policy controls.

# MCP Layer

The MCP Layer provides governed access to external tools, APIs, and enterprise systems.

## MCP Server Registry

The MCP Server Registry maintains approved MCP servers, tool capabilities, owners, usage constraints, and access policies.

## MCP Gateway

The MCP Gateway brokers access from AI-enabled workflow tasks to approved tools and systems.

## Guardrails and Policies

MCP guardrails enforce authentication, authorization, parameter validation, tool allow-lists, rate limits, and execution constraints.

Tool permissions should be scoped per AI-enabled workflow task and workflow state.

# Evaluation Layer

The Evaluation Layer provides oversight, quality assurance, and runtime protection for the loan approval workflow.

## Feedback Engine

The Feedback Engine captures feedback from loan officers, compliance reviewers, automated checks, workflow outcomes, and downstream monitoring.

Feedback should be linked to the relevant workflow instance, AI-enabled task, model output, tool call, decision gate, and human review action.

## Human Supervision

Human supervision is used for medium-risk, low-confidence, conflicting, high-impact, or policy-sensitive outcomes.

Human reviewers can approve, reject, amend, request more information, or escalate the workflow.

## Runtime Protection

Runtime protection can block unsafe task execution, prevent unauthorized tool calls, require additional validation, or route the workflow to human review.

# Observability Layer

The Observability Layer captures end-to-end evidence across the workflow engine, AI-enabled tasks, models, tools, gateways, and downstream systems.

## Logs

Logs capture workflow starts, task assignments, AI task inputs and outputs, model calls, tool calls, policy decisions, human review actions, and final outcomes.

## Traces

Traces link the full loan approval lifecycle across application intake, gateway, workflow execution, AI-enabled tasks, model calls, tool calls, decision gates, human review, e-signature, and disbursement.

## Metrics

Metrics include task latency, workflow duration, throughput, success rate, failure rate, retry rate, model usage, tool usage, token or consumption metrics, human review rate, and escalation rate.

## Events

Events capture workflow transitions including application submitted, documents extracted, fraud check completed, credit risk assessed, decision gate reached, human review requested, agreement generated, signed, and disbursed.

## Anomaly Detection

Anomaly detection identifies unusual task behaviour, model usage spikes, tool usage anomalies, workflow bottlenecks, unexpected escalation rates, and unusual decision patterns.

## Correlation Engine

The Correlation Engine links logs, traces, metrics, events, task identifiers, model calls, tool calls, and workflow instance IDs.

## Observability Dashboard

Observability dashboards show workflow task performance, AI task consumption, tool consumption, model usage, latency, errors, throughput, and end-to-end workflow performance.

# Loan Approval Reference Flow

1. Applicant submits the loan application and supporting documents.
2. The loan origination application sends the request through the Agent Gateway.
3. The workflow engine starts the loan approval workflow.
4. The workflow engine invokes the Document Intelligence Task to extract and validate application data.
5. The workflow engine runs automated validation checks for missing, invalid, or low-confidence fields.
6. The workflow engine invokes the Fraud Detection Task.
7. Fraud-flagged cases are routed to Compliance Review.
8. Credit score data is retrieved through approved APIs.
9. The workflow engine invokes the Credit Risk Assessment Task.
10. A deterministic decision gate combines credit score, AI risk output, policy thresholds, and workflow evidence.
11. Low-risk applications may be auto-approved.
12. Medium-risk or uncertain applications are routed to loan officer review.
13. High-risk applications may be rejected according to policy.
14. Auto-approved applications are routed to Dynamic Pricing.
15. Approved terms are sent to Document Generation.
16. The loan agreement is sent for e-signature.
17. Signed agreements trigger disbursement through approved downstream systems.
18. Observability captures task, model, tool, workflow, and decision telemetry throughout the process.

# AIGF Control Mapping

AIGF controls apply across:

- Application intake
- Agent Gateway
- Workflow orchestration
- AI-enabled workflow task execution
- Model access
- Tool and API access
- Data handling
- Decision gates
- Human review
- Observability and audit

# Relationship to CALM

This solution architecture is intended to be codified in CALM after the solution-level architecture is agreed.

The CALM representation should capture:

- The reference architecture layers
- The workflow engine as the workflow orchestrator
- AI-enabled workflow tasks as specialist worker-agent implementations
- Task-to-tool relationships
- Model access relationships
- Data and knowledge dependencies
- Human review paths
- Decision gates
- Observability flows to dashboards
- AIGF control boundaries
