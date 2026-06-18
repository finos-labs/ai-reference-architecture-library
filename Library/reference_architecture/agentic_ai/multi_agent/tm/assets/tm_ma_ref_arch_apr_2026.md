# Threat Model: Multi-Agent Reference Architecture (Component Level)

This is the **May 2026** revision of the FINOS Multi-Agent Reference Architecture threat model. It supersedes the layer-level mapping in [`tm_ma_ref_arch_apr_2026.md`](./tm_ma_ref_arch_apr_2026.md) by re-anchoring each threat to the **specific component** it lands on, and by crosswalking every threat and control to the **FINOS AI Governance Framework (AIGF)**.

The model carries forward, unchanged, the 58 threats and 36 controls from the April revision. What this revision adds is, for each threat, the architecture component(s) it targets and the AIGF risk(s) it represents; and for each control, the AIGF mitigation(s) it implements. Threat, control, and description text is preserved verbatim so the two documents remain cross-referenceable. Component IDs are CALM `unique-id`s from [`ma_ref_arch_jan_2026.calm.json`](../ra/ma_ref_arch_jan_2026.calm.json).

**How to read it.** [Section 1](#1-threats-by-component) is the core: threats grouped by the architectural layer and the component they land on, each with its controls and its AIGF risk(s). [Section 2](#2-controls--aigf-mitigations) defines each control and the AIGF mitigation(s) it implements. The [Appendix](#appendix-aigf-catalogue) lists the AIGF risk and mitigation IDs in full.

**AIGF provenance.** All `ri-*` (risk) and `mi-*` (mitigation) identifiers and titles are taken verbatim from the authoritative FINOS source and validated against the [FINOS AI Governance MCP server](https://github.com/finos/aigf-mcp-server), which serves the live catalogue from [`finos/ai-governance-framework`](https://github.com/finos/ai-governance-framework) (rendered at [air-governance-framework.finos.org](https://air-governance-framework.finos.org)). This is **AIGF v2**, which adds the agentic-AI risks (`ri-24`…`ri-29`) and mitigations (`mi-18`…`mi-23`). The catalogue holds 23 risks and 23 mitigations. The crosswalk itself is an engineering best-fit judgement and may assign more than one risk/mitigation per item; it should be reviewed by a governance owner before use in a formal compliance artefact.

---

## 1. Threats by Component

For each threat: the component(s) it lands on, its controls (defined in [Section 2](#2-controls--aigf-mitigations)), and the AIGF risk(s) it represents.

### User Interaction Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-UIL-01 | Malicious User Input | `application`, `agent-gateway-guardrails` | A user intentionally submits crafted inputs, including prompt injection, jailbreak attempts, or instruction-carrying payloads, to exploit the agent system or cause harmful downstream actions. Applies to interactive users and automated calling systems. | C1, C2, C3 | ri-10, ri-24 |
| T-UIL-02 | User Impersonation | `user`, `application` | An attacker compromises or forges credentials to impersonate a legitimate user, inheriting their permissions, context, and agent-delegated authority. | C4, C5 | ri-24, ri-29 |
| T-UIL-03 | Application Session Hijacking | `application` | An attacker intercepts or steals a valid application session token to issue requests to the agent system under an authenticated user's identity. | C4, C5, C9 | ri-24, ri-29 |
| T-UIL-04 | Denial of Service | `application`, `agent-gateway` | An attacker floods the application with a high volume of requests to exhaust system capacity, degrade response quality, or trigger runaway agent execution chains. | C6, C7 | ri-7 |
| T-UIL-05 | Insecure Output Handling | `application`, `secure-execution` | An agent generates output containing executable content, such as XSS payloads or script injection, or sensitive data that is returned to the user interface without sanitisation. | C19, C23 | ri-20, ri-22 |
| T-UIL-06 | HITL Workflow Manipulation | `human-supervision`, `application` | An attacker manipulates the human-in-the-loop approval interface through social engineering, UI spoofing, or prompt crafting so that a reviewer unknowingly approves a harmful or policy-violating agent action. | C3, C9, C26 | ri-24, ri-22 |

### Agent Gateway Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-AGL-01 | Agent Registry Poisoning | `agent-registry` | An attacker gains write access to the Agent Registry and inserts a malicious agent entry so the gateway routes legitimate requests to an agent under the attacker's control. | C8, C9, C10 | ri-28, ri-24 |
| T-AGL-02 | Gateway Bypass | `agent-gateway`, `unified-agent-runtime` | An attacker discovers a direct network path to an agent instance, bypassing the gateway and all associated authentication, authorisation, and policy enforcement. | C11, C12 | ri-24 |
| T-AGL-03 | Guardrail and Policy Evasion | `agent-gateway-guardrails` | An attacker crafts requests that are syntactically valid but semantically designed to evade the gateway's guardrail policy rules, passing through controls while carrying malicious or out-of-scope instructions. | C5, C13 | ri-10, ri-24 |
| T-AGL-04 | Agent Identity Spoofing at Gateway | `agent-gateway`, `agent-gateway-guardrails` | An attacker presents a forged or stolen agent identity to the gateway to inherit the permissions, routing rules, or trust level of a legitimate registered agent. Particularly relevant where agent-to-agent calls pass through the gateway. | C12, C36 | ri-28, ri-24 |

### Agent Layer — Collaboration Patterns

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-AL-01 | Supervisor Agent Compromise | `supervisor-worker` | An attacker gains control of the supervisor agent in a Supervisor/Worker pattern, enabling them to issue arbitrary instructions to all subordinate worker agents and control the entire agent graph from a single point. | C11, C14, C16 | ri-28, ri-24 |
| T-AL-02 | Goal Manipulation via Skill Routing | `skills-based-routing`, `agent-registry` | An attacker exploits the Skills-Based Routing mechanism by crafting requests that cause the router to dispatch a sensitive task to a low-trust or compromised agent. | C8, C10, C15 | ri-28, ri-14, ri-24 |
| T-AL-03 | Agent-as-a-Tool Abuse | `agent-as-a-tool` | An attacker exploits the Agent-as-a-Tool pattern to cause a high-privilege agent to invoke a compromised or malicious agent as a sub-tool, granting it access to the caller's permissions and context. | C9, C16, C36 | ri-28, ri-25 |
| T-AL-04 | Agent Collusion | `supervisor-worker`, `agent-as-a-tool`, `collaboration-handoff` | Two or more compromised agents coordinate to circumvent a control that neither could bypass independently. A representative example is one agent generating a recommendation and a colluding agent approving it, bypassing a four-eyes control. | C5, C9, C16 | ri-28, ri-24 |

### Agent Layer — Unified Agent Runtime

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-AL-05 | Goal Manipulation | `unified-agent-runtime`, `state-management` | An attacker manipulates an agent's goal or objective through crafted input so that the agent pursues an unintended task while appearing to operate normally. | C1, C14, C15 | ri-10, ri-14 |
| T-AL-06 | Indirect Prompt Injection | `unified-agent-runtime`, `short-term-memory`, `web-search-tool`, `mcp-client` | An attacker embeds malicious instructions in external data consumed by the agent, including retrieved documents, tool responses, web content, and counterparty messages, to manipulate agent reasoning without any malicious action by the human user. | C2, C14, C33 | ri-10, ri-25 |
| T-AL-07 | Multi-Hop Prompt Injection | `supervisor-worker`, `agent-as-a-tool`, `collaboration-handoff` | A successful injection in a sub-agent propagates malicious instructions upstream to the supervisor, which then propagates them to the rest of the agent graph. The initial injection point may be low-privilege, but the impact extends to the entire graph. | C2, C14, C16, C36 | ri-10, ri-28 |
| T-AL-08 | Excessive Agency and Scope Expansion | `unified-agent-runtime`, `secure-execution`, `human-supervision`, `runtime-protection` | An agent autonomously expands its own scope, takes irreversible real-world actions, or executes an action chain whose cumulative effect was not authorised even though each individual action appeared permitted. In FSI deployments, this is the vector most likely to produce a regulatory incident. | C3, C15, C26 | ri-18, ri-24 |
| T-AL-09 | Agent Resource Exhaustion | `unified-agent-runtime`, `secure-execution` | An attacker tricks an agent into executing a computationally expensive, long-running, or infinitely recursive task that exhausts CPU, memory, or time quotas and starves other agents of execution capacity. | C15, C22 | ri-7 |
| T-AL-10 | Secrets Exfiltration from Agent Runtime | `secure-execution`, `unified-agent-runtime` | A compromised agent reads environment variables, mounted secrets, service account tokens, or credentials accessible within its execution environment and exfiltrates them to an attacker-controlled endpoint. | C14, C28, C29 | ri-29 |
| T-AL-11 | Adaptive Learning Poisoning | `adaptive-learning` | An attacker manipulates the execution outcomes, user feedback, or error rate signals used by the Adaptive Learning component to cause the system to learn degraded prompt templates, miscalibrated agent configurations, or poor tool selection strategies. Unlike one-time feedback manipulation, this attack corrupts the persistent learning state. | C9, C15, C27 | ri-9, ri-27 |
| T-AL-12 | Workspace File System Abuse | `workspace-file-system`, `io-tool` | A compromised agent writes malicious content to the shared Workspace File System that is subsequently read and acted upon by another agent or human user, propagating compromise through the file layer rather than the messaging layer. | C8, C9, C14, C19 | ri-27, ri-25 |
| T-AL-13 | State Hijacking via Pause and Resume | `state-management` | An attacker manipulates the serialised task state during a pause or handoff operation so that when the task is resumed, the agent operates with attacker-controlled goal state, memory, or credentials. | C9, C12, C20 | ri-27 |
| T-AL-14 | Inter-Agent Compromise via Shared State | `collaboration-handoff`, `state-management`, `short-term-memory`, `long-term-memory` | A compromised agent attacks, manipulates, or exfiltrates data from peer agents in the same environment by exploiting shared memory, state, or collaboration and handoff channels. | C5, C11, C14 | ri-28, ri-27 |

### Agent Layer — Tools

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-AL-15 | Shell Tool Abuse | `shell-tool`, `secure-execution` | A compromised agent invokes the Shell Tool with attacker-controlled command arguments to execute arbitrary system commands, write malicious files, or escalate privileges within the runtime. | C1, C14, C17 | ri-25, ri-24 |
| T-AL-16 | I/O Tool Abuse | `io-tool`, `workspace-file-system` | A compromised agent uses the I/O Tool to read files outside its designated workspace paths, exfiltrate data to the workspace for collection by another process, or write malicious content that poisons subsequent agent reads. | C8, C9, C14 | ri-25, ri-27 |
| T-AL-17 | Web Search Tool Manipulation | `web-search-tool` | An attacker places adversarially crafted content at URLs likely to be returned by the Web Search Tool so that the agent retrieves and acts on attacker-controlled information. The agent has no inherent basis to distrust search results, making this a reliable indirect injection vector. | C2, C28, C33 | ri-25, ri-10 |
| T-AL-18 | MCP Client Misuse | `mcp-client` | A compromised agent exploits the MCP Client to invoke unauthorised MCP servers or pass sensitive runtime data as tool call parameters to an attacker-controlled endpoint. | C28, C32, C35 | ri-25, ri-26 |

### Agent Layer — Memory

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-AL-19 | Context Window Poisoning | `short-term-memory`, `in-session-context-manager` | An attacker injects adversarial content into the agent's in-session context window via tool responses, retrieved documents, or inter-agent messages so that the in-session context manager propagates the malicious content through summarisation or trimming into the agent's active reasoning. | C2, C14, C33 | ri-10, ri-25 |
| T-AL-20 | Long-Term Memory Poisoning | `long-term-memory`, `session-summaries`, `user-task-personalization` | An attacker injects crafted content into session summaries or user and task personalisation stores that persists across sessions and systematically biases future agent behaviour, affecting every future session that retrieves the poisoned entries. | C9, C18, C34 | ri-27, ri-9 |
| T-AL-21 | Cross-Session Memory Leakage | `long-term-memory`, `session-summaries`, `user-task-personalization` | Information from one user's or session's interactions, stored in session summaries or personalisation data, is retrieved and exposed in a subsequent session belonging to a different user. Particularly relevant in multi-tenant deployments. | C8, C24, C34 | ri-28, ri-1 |

### Knowledge Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-KL-01 | Data Poisoning | `source-bases`, `vector-dbs` | An attacker injects false, misleading, or maliciously crafted information into source bases or vector databases so that agents ground their reasoning in attacker-controlled content. Includes targeted poisoning of specific document types and bulk poisoning of ingestion pipelines. | C8, C18 | ri-9 |
| T-KL-02 | RAG Retrieval Manipulation | `vector-dbs` | An attacker crafts content that scores highly in semantic similarity search so it is consistently retrieved and injected into the agent context ahead of legitimate authoritative content, without requiring write access to the knowledge base. This threat exploits the retrieval mechanism itself. | C15, C33 | ri-9, ri-19 |
| T-KL-03 | Data Leakage from Knowledge Layer | `source-bases`, `vector-dbs` | An agent retrieves and exposes data from the knowledge layer to an unauthorised caller through over-broad retrieval queries, or is manipulated into returning data beyond the scope of the legitimate request. | C8, C19, C24 | ri-1, ri-2 |
| T-KL-04 | Embedding Inversion Attack | `vector-dbs` | An attacker gains access to the raw vector database and reconstructs original source documents from stored embeddings, exposing knowledge base content that may be confidential or regulated, without requiring access to the original document store. | C8, C24 | ri-2 |

### LLM Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-LLM-01 | Malicious Model Registration | `model-registry`, `llm-zoo` | An attacker registers a backdoored or adversarially fine-tuned model in the Model Registry that behaves normally under standard inputs but produces attacker-controlled outputs for specific trigger sequences. | C10, C25, C30 | ri-8, ri-26 |
| T-LLM-02 | Model Theft | `model-registry`, `llm-zoo` | An attacker gains unauthorised access to the Model Registry or inference infrastructure and exfiltrates proprietary model weights, causing intellectual property loss and enabling offline adversarial analysis. | C5, C8, C24 | ri-23 |
| T-LLM-03 | Prompt Injection at LLM Inference | `llm-gateway`, `llm-guardrails`, `llm-zoo` | An attacker crafts a prompt that causes the LLM to ignore its system instructions, override guardrails at the inference layer, or generate policy-violating output. May be delivered directly by a user or indirectly through content that has reached the inference endpoint. | C2 | ri-10 |
| T-LLM-04 | System Prompt Leakage | `llm-zoo`, `llm-guardrails` | An attacker manipulates the LLM into revealing its system prompt, which may contain confidential workflow logic, tool configurations, or security controls that can be exploited in subsequent attacks. | C2, C13, C29 | ri-1, ri-10 |
| T-LLM-05 | LLM Gateway Guardrail Bypass | `llm-gateway`, `llm-guardrails` | An attacker crafts inputs or exploits misconfiguration to bypass the LLM Gateway's guardrails, including input validation, output filtering, and access controls, causing the gateway to forward harmful content or unauthorised requests to the model. | C5, C13, C21 | ri-10, ri-24 |

### MCP Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-MCP-01 | MCP Registry Poisoning | `mcp-server-registry` | An attacker gains write access to the MCP Server Registry and inserts a malicious server entry so that agents connect to an attacker-controlled server when they believe they are using an authorised enterprise tool. | C8, C9, C10 | ri-26 |
| T-MCP-02 | Compromised MCP Server | `mcp-zoo` | An attacker compromises a legitimate MCP server to intercept tool call requests, manipulate responses returned to the agent, or pivot to connected enterprise systems using the server's service account credentials. | C10, C11, C12, C24 | ri-26, ri-25 |
| T-MCP-03 | MCP Gateway Bypass | `mcp-gateway`, `mcp-zoo` | An attacker discovers a direct network path to an MCP server that bypasses the MCP Gateway and all associated authentication, authorisation, and policy enforcement. | C11, C12 | ri-24, ri-26 |
| T-MCP-04 | Tool Name Shadowing | `mcp-server-registry`, `mcp-gateway` | An attacker registers a malicious MCP server that declares a tool with the same name as a legitimate authorised tool on a different server so that routing resolves to the malicious server when an agent requests the tool by name. | C8, C9, C10 | ri-25, ri-26 |
| T-MCP-05 | Data Exfiltration via Tool Parameters | `mcp-client`, `mcp-gateway`, `mcp-guardrails` | An agent is manipulated into passing sensitive data as parameters to an MCP tool call that routes them to an attacker-controlled or unapproved third-party server. The data leaves the trust boundary embedded in the call parameters. | C19, C28, C32, C35 | ri-1, ri-25 |
| T-MCP-06 | MCP Capability Escalation | `mcp-server-registry`, `mcp-zoo` | A registered MCP server advertises capabilities or data access permissions broader than those approved in its security review, leading agents to request operations they are not authorised to perform. | C8, C10, C13 | ri-26, ri-24 |

### Evaluation Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-EVL-01 | Feedback Manipulation | `feedback-engine` | An attacker provides systematically false or adversarially crafted feedback to the Feedback Engine so that the system learns incorrect behaviours, lowers safety thresholds, or increases confidence in attacker-desired outputs over time. | C5, C27 | ri-9, ri-27 |
| T-EVL-02 | Bypassing Human Supervision | `human-supervision` | An attacker identifies a code path, configuration state, or race condition that allows actions requiring human approval to execute without going through the Human Supervision workflow. In FSI deployments, this directly produces governance and regulatory failures. | C9, C26 | ri-24, ri-22 |
| T-EVL-03 | Runtime Protection Evasion | `runtime-protection` | An attacker crafts agent outputs or action sequences that individually pass Runtime Protection monitoring rules but in aggregate represent a policy violation or unsafe action, exploiting the stateless nature of most rule-based runtime monitors. | C5, C15, C21 | ri-24, ri-18 |

### Observability Layer

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-OBL-01 | Log Tampering | `logs` | An attacker modifies or deletes existing log entries after gaining access to an agent or gateway to conceal activity, obstruct forensic investigation, or prevent audit trail reconstruction. | C9, C20 | ri-22 |
| T-OBL-02 | Log Injection | `logs`, `correlation-engine` | A compromised agent crafts log entries containing false attribution, forged agent identities, fabricated timestamps, or content designed to confuse Correlation Engine rules or mislead forensic investigators. The log store is written correctly; the content itself is adversarial. | C9, C20 | ri-22, ri-17 |
| T-OBL-03 | Alert Fatigue | `events`, `anomaly-detection`, `correlation-engine` | An attacker generates a sustained high volume of low-fidelity alerts to desensitise operators so that genuine high-severity events are missed or deprioritised. This may also occur organically through poor alert tuning. | C21 | ri-22 |
| T-OBL-04 | Trace Poisoning | `traces` | A compromised agent injects fabricated or tampered trace spans into the distributed trace for a request so that operators reconstruct a false view of the request lifecycle, masking the actual path taken by a compromised request. | C9, C20 | ri-17, ri-22 |
| T-OBL-05 | Anomaly Detection Evasion | `anomaly-detection`, `correlation-engine` | An attacker operates at or just below anomaly detection thresholds through techniques such as slow exfiltration, incremental privilege escalation, or gradual drift in agent behaviour, maintaining persistence without triggering the Anomaly Detection component. | C5, C15, C21 | ri-22, ri-24 |

> The Observability-layer threats (`T-OBL-*`) have no single dedicated AIGF *risk*; they undermine the integrity of governance evidence and so map to **ri-22 (Regulatory Compliance and Oversight)** and **ri-17 (Lack of Explainability)**. In AIGF terms they primarily attack the *mitigations* **mi-4 (AI System Observability)** and **mi-21 (Agent Decision Audit and Explainability)** rather than introducing a new risk.

### Cross-Layer and FSI-Specific Threats

These threats span multiple components; their controls must be coordinated across every component listed.

| Threat ID | Threat | Component(s) | Description | Controls | AIGF Risk(s) |
|---|---|---|---|---|---|
| T-XL-01 | MNPI Leakage Across Agent Boundaries | `supervisor-worker`, `agent-as-a-tool`, `collaboration-handoff`, `mcp-client`, `source-bases`, `vector-dbs` | Material Non-Public Information handled by an agent authorised for restricted data is passed, intentionally or inadvertently, to an agent or tool not authorised to handle it. In a Supervisor/Worker pattern, a supervisor with broad data access may pass MNPI to a worker whose permitted data domains do not include MNPI. This constitutes a wall-crossing violation and a potential MAR breach. | C8, C16, C19, C32 | ri-28, ri-1, ri-22 |
| T-XL-02 | Regulatory Reporting Manipulation | `unified-agent-runtime`, `human-supervision`, `feedback-engine` | An agent that produces output used for regulatory submissions generates false, altered, or incomplete output through a successful attack or goal manipulation, and the result is submitted to a regulator without the manipulation being detected. | C3, C9, C26 | ri-22, ri-4 |
| T-XL-03 | Supply Chain Compromise | `model-registry`, `llm-zoo`, `mcp-server-registry`, `mcp-zoo`, `agent-registry` | An attacker introduces a backdoored model, library, dataset, or MCP server into the system through the CI/CD or onboarding pipeline, creating a persistent compromise that bypasses runtime controls. | C25, C30, C31 | ri-26, ri-8 |
| T-XL-04 | Cascade Failure Propagation | `unified-agent-runtime`, `llm-gateway`, `llm-zoo`, `mcp-gateway`, `mcp-zoo`, `source-bases`, `vector-dbs` | A failure, resource exhaustion, or compromise in one agent or dependency, such as an MCP server, LLM endpoint, or knowledge source, propagates through agent dependencies and results in system-wide unavailability or degraded behaviour. | C15, C21, C22 | ri-7 |

---

## 2. Controls → AIGF Mitigations

Each control, its definition, and the AIGF mitigation(s) it implements. Mitigation titles are in the [Appendix](#appendix-aigf-catalogue).

| Control | Control Description | AIGF Mitigation(s) |
|---|---|---|
| C1 | Implement input validation libraries and custom rules at the application layer | mi-3, mi-17 |
| C2 | Use a specialised AI security tool, integrated as part of a gateway (Agent, MCP, or LLM) or as a standalone firewall, to inspect, validate, and sanitise prompts and responses for content-based threats | mi-17, mi-3 |
| C3 | Design workflows to require human approval for high-risk actions | mi-11 |
| C4 | Integrate with an enterprise Identity Provider (IDP) enforcing Multi-Factor Authentication (MFA) | mi-12 |
| C5 | Feed user activity and agent logs into a security analytics platform (SIEM) for behavioural analytics and anomaly detection | mi-4, mi-9 |
| C6 | Configure rate limiting and throttling on API endpoints | mi-8, mi-9 |
| C7 | Deploy a Web Application Firewall (WAF) with rulesets to block common attack vectors | mi-17, mi-8 |
| C8 | Use a Policy-as-Code engine to enforce Attribute-Based Access Control (ABAC) policies for write access | mi-12, mi-16 |
| C9 | Log all critical changes to a centralised, write-once, read-many (WORM) system | mi-4, mi-21 |
| C10 | Implement automated configuration scanning to detect unauthorised changes | mi-5 |
| C11 | Use platform-native network policies (Kubernetes network policies, security groups) to enforce micro-segmentation | mi-22 |
| C12 | Enforce mutual TLS (mTLS) for all connections using strong, verifiable workload identities (SPIFFE SVIDs) | mi-22, mi-23 |
| C13 | Manage and version-control all security policies (authorisation, content, behaviour) as code in a version control system | mi-5, mi-17 |
| C14 | Use sandboxing or containerisation technologies to isolate agent processes and tool execution | mi-22 |
| C15 | Implement runtime monitoring to detect and alert on anomalous agent behaviour or goal deviation | mi-4, mi-9 |
| C16 | Ensure child agents are created with a scoped, delegated workload identity and a subset of the parent agent's permissions | mi-18, mi-23 |
| C17 | Grant just-in-time, task-scoped permissions for tool execution | mi-18 |
| C18 | Implement automated data validation and sanitisation pipelines for knowledge sources | mi-2, mi-6 |
| C19 | Integrate with Data Loss Prevention (DLP) solutions to scan agent outputs for sensitive data | mi-1 |
| C20 | Ensure logs contain full cryptographic workload identity and rich contextual details for forensic investigation | mi-4, mi-21 |
| C21 | Implement automated response playbooks (SOAR) for high-confidence alerts | mi-9 |
| C22 | Implement resource quotas and limits (CPU, memory, execution time) within the agent runtime environment | mi-8 |
| C23 | Sanitise all agent-generated output to remove or neutralise malicious code before it is displayed to users or passed to other systems | mi-3, mi-17 |
| C24 | Encrypt sensitive data, including models, both at rest and in transit | mi-14 |
| C25 | Implement a strict vetting and scanning process for all new models and components before they are registered in the platform | mi-5, mi-20 |
| C26 | Enforce strict, non-bypassable workflows for tasks that require human supervision | mi-11 |
| C27 | Implement a multi-source feedback engine that aggregates and weights signals from diverse sources to generate a trusted score for agent interactions | mi-11, mi-15 |
| C28 | Implement network egress filtering to restrict agent outbound connections to an approved destination allowlist | mi-1, mi-22 |
| C29 | Use a dedicated secrets management platform for all credentials, API keys, and certificates; never inject secrets as environment variables accessible to the agent process | mi-23 |
| C30 | Enforce cryptographic signing and integrity verification for all registered models; validate signatures at load time and reject any model whose signature cannot be verified | mi-10, mi-20 |
| C31 | Maintain a Software Bill of Materials (SBOM) for all agent components, dependencies, and models; integrate SBOM generation and vulnerability scanning into the CI/CD pipeline | mi-20, mi-5 |
| C32 | Enforce data minimisation at the agent runtime layer; pass only the data fields required for each tool call and strip all fields not needed for the immediate task before dispatch | mi-1, mi-19 |
| C33 | Apply RAG-specific validation; validate and sanitise retrieved documents before injection into the agent context window and monitor retrieval patterns for anomalous query distributions | mi-2, mi-19 |
| C34 | Enforce strict cross-session isolation in long-term memory; cryptographically bind all memory entries to the originating user and session identity | mi-22, mi-12 |
| C35 | Enforce strict parameter schema validation for all MCP tool calls; reject any call where parameters contain data from domains not authorised for that tool | mi-19, mi-20 |
| C36 | Require signed Agent Card verification for all agent-to-agent calls; reject connections from agents presenting unverifiable, expired, or revoked identities; use a short-lived credential rotation policy | mi-23, mi-22 |

> AIGF mitigations with no corresponding control in this model — **mi-7 (Legal and Contractual Frameworks)** and **mi-13 (Citations and Source Traceability)** — are organisational/process mitigations outside the scope of these technical controls and are listed for completeness only.

---

## Appendix: AIGF Catalogue

Reference IDs and titles, verbatim from the FINOS AI Governance MCP server (AIGF v2: 23 risks, 23 mitigations).

### AIGF Risks

| Risk | Title |
|---|---|
| ri-1 | Information Leaked to Hosted Model |
| ri-2 | Information Leaked to Vector Store |
| ri-4 | Hallucination and Inaccurate Outputs |
| ri-5 | Foundation Model Versioning |
| ri-6 | Non-Deterministic Behaviour |
| ri-7 | Availability of Foundational Model |
| ri-8 | Tampering With the Foundational Model |
| ri-9 | Data Poisoning |
| ri-10 | Prompt Injection |
| ri-14 | Inadequate System Alignment |
| ri-16 | Bias and Discrimination |
| ri-17 | Lack of Explainability |
| ri-18 | Model Overreach / Expanded Use |
| ri-19 | Data Quality and Drift |
| ri-20 | Reputational Risk |
| ri-22 | Regulatory Compliance and Oversight |
| ri-23 | Intellectual Property (IP) and Copyright |
| ri-24 | Agent Action Authorization Bypass |
| ri-25 | Tool Chain Manipulation and Injection |
| ri-26 | MCP Server Supply Chain Compromise |
| ri-27 | Agent State Persistence Poisoning |
| ri-28 | Multi-Agent Trust Boundary Violations |
| ri-29 | Agent-Mediated Credential Discovery and Harvesting |

### AIGF Mitigations

| Mitigation | Title |
|---|---|
| mi-1 | AI Data Leakage Prevention and Detection |
| mi-2 | Data Filtering From External Knowledge Bases |
| mi-3 | User/App/Model Firewalling/Filtering |
| mi-4 | AI System Observability |
| mi-5 | System Acceptance Testing |
| mi-6 | Data Quality & Classification/Sensitivity |
| mi-7 | Legal and Contractual Frameworks for AI Systems |
| mi-8 | Quality of Service (QoS) and DDoS Prevention for AI Systems |
| mi-9 | AI System Alerting and Denial-of-Wallet (DoW) Spend Monitoring |
| mi-10 | AI Model Version Pinning |
| mi-11 | Human Feedback Loop for AI Systems |
| mi-12 | Role-Based Access Control for AI Data |
| mi-13 | Providing Citations and Source Traceability for AI-Generated Information |
| mi-14 | Encryption of AI Data at Rest |
| mi-15 | Using LLMs for Automated Evaluation (LLM-as-a-Judge) |
| mi-16 | Preserving Source Data Access Controls in AI Systems |
| mi-17 | AI Firewall Implementation and Management |
| mi-18 | Agent Authority Least-Privilege Framework |
| mi-19 | Tool Chain Validation and Sanitization |
| mi-20 | MCP Server Security Governance |
| mi-21 | Agent Decision Audit and Explainability |
| mi-22 | Multi-Agent Isolation and Segmentation |
| mi-23 | Agentic System Credential Protection Framework |

---

*May 2026 component-level revision of the Multi-Agent Reference Architecture threat model. Threat and control content preserved verbatim from `tm_ma_ref_arch_apr_2026.md`; component identifiers from `ma_ref_arch_jan_2026.calm.json`; AIGF risk/mitigation identifiers validated against the FINOS AI Governance MCP server (`finos/aigf-mcp-server`, serving `finos/ai-governance-framework` v2).*
