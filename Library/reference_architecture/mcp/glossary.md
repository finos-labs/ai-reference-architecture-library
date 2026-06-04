# Glossary of Key Terms

## Agentic AI
An AI system that can independently reason, plan, adapt, and pursue objectives without direct human intervention over an extended time horizon, interacting with tools and other agents.

## Agent-to-Agent (A2A)
A protocol for enabling communication between autonomous agents, solving for long-running tasks and state management across multiple agents. A2A and MCP are complementary protocols.

## Capability Negotiation
The process during MCP session initialization where the client and server exchange and agree upon the set of features each supports. Only mutually declared capabilities are activated for the session.

## Composable MCP
A gateway pattern that defines custom MCP tools as multi-step pipelines chaining HTTP calls and MCP tool calls together, with each step referencing outputs from previous steps.

## Confused Deputy Problem
A security vulnerability where an intermediary (the "deputy") uses its own credentials or permissions on behalf of a caller in ways the caller did not intend. In MCP, this occurs when a server forwards a client's OAuth token to an upstream API.

## Cross-Server Isolation
The architectural principle that each MCP client maintains an exclusive connection to a single server, preventing servers from accessing data from other servers without explicit gateway mediation.

## Dynamic Client Registration (DCR)
An OAuth mechanism (RFC 7591) allowing clients to register themselves with an authorization server at runtime, rather than requiring pre-registration.

## Elicitation
An MCP client capability that allows servers to request structured additional information from users during the course of a tool or resource operation, expressed as JSON Schema-defined forms.

## Host (MCP Host)
The AI application that coordinates and manages one or more MCP clients. In this architecture, the Host role is fulfilled by the MCP Gateway Layer.

## JSON-RPC 2.0
The remote procedure call protocol used as the foundation for MCP message exchange. All MCP messages are framed as JSON-RPC 2.0 requests, responses, or notifications.

## Legacy Broker
An MCP-compliant server facade that translates MCP primitives into legacy backend operations for systems that cannot implement MCP natively.

## Model Context Protocol (MCP)
An open protocol that standardizes how AI applications connect to external data sources, tools, and services. Originally published by Anthropic and subsequently donated to the Agentic AI Foundation under the Linux Foundation.

## MCP Client
In the MCP specification, a protocol-level object that a Host creates to maintain a dedicated 1:1 connection to a single MCP server. This architecture calls these objects **Connections** (see MCP Connection Layer) to avoid confusion with the colloquial meaning of "client" as the end-user application.

## MCP Connection Layer
The architectural layer containing the pool of isolated, stateful protocol connection instances that the MCP Gateway (Host) creates and manages. Each connection maintains an exclusive 1:1 session with a single MCP server.

## MCP Gateway
An enterprise-grade control plane that subsumes the MCP Host role, providing centralized server registry, session-aware routing, policy enforcement, consent management, and multi-tenancy isolation.

## MCP Server
A program that provides context and capabilities to MCP clients through standardized protocol interfaces, exposing tools, resources, and prompts.

## Per-Client Tool Virtualization
The gateway capability to dynamically adjust the set of tools and capabilities exposed to each client session based on identity, policy, and tenancy, preventing information leakage about restricted backend capabilities.

## PKCE (Proof Key for Code Exchange)
An OAuth extension (RFC 7636) that mitigates authorization code interception attacks by requiring a code verifier. MCP mandates S256 as the challenge method.

## Prompt Injection
An attack where adversarial instructions are embedded in tool descriptions, resource content, or prompt templates to alter model behavior, cause data exfiltration, or bypass safety controls.

## Protected Resource Metadata (PRM)
A discovery mechanism (RFC 9728) where an MCP server publishes metadata identifying its authorization server, supported scopes, and canonical resource URI.

## Resource Indicators (RFC 8707)
A mechanism to bind OAuth tokens to a specific resource (MCP server), preventing token reuse across different servers.

## Sampling
An MCP client capability that allows servers to request LLM inference calls from the host application, enabling server-side agentic behaviors while preserving gateway mediation.

## Shadow IT (MCP)
The risk created when users connect directly to public SaaS MCP servers (e.g., GitHub, Databricks, Slack) without corporate identity, security, or observability controls.

## stdio Transport
The local transport mechanism for MCP where the server runs as a subprocess and communicates via standard input/output streams. Used for local, in-process servers.

## Streamable HTTP Transport
The remote transport mechanism for MCP using HTTP POST for client-to-server messages and optional Server-Sent Events for streaming. Replaces the legacy HTTP+SSE transport.

## Task (MCP Task)
An abstraction for long-running, asynchronous operations exposed by MCP servers as trackable units of work with status querying and result retrieval.

## Token Passthrough
The prohibited practice of an MCP server forwarding an OAuth access token received from a client to an upstream API. Each hop in the service graph must authenticate independently.

## Tool Poisoning
An attack where a malicious or compromised MCP server modifies its tool descriptions or implementations after initial registration to introduce harmful behavior.

## Virtual MCP Backend
A gateway pattern that federates multiple MCP servers behind a single endpoint using label selectors, allowing clients to access tools from multiple servers through one connection.
