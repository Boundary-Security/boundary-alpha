<p align="center">
  <img src="assets/boundary-logo.png" alt="Boundary Security" width="150">
</p>

<h1 align="center">Boundary</h1>

<p align="center">
  <strong>Predictive security enforcement for autonomous AI agents and MCP systems.</strong>
</p>

<p align="center">
  Stop dangerous agent behavior before the dangerous action is requested.
</p>

<p align="center">
  <a href="https://boundarysec.com">Website</a>
  ·
  <a href="https://boundarysec.com/#alpha">Request Private Alpha</a>
  ·
  <a href="mailto:info@boundarysec.com">Contact</a>
</p>

---

## What is Boundary?

Most authorization systems ask:

> **Is this action allowed right now?**

Boundary asks an additional question:

> **If this action is allowed now, what dangerous future states become reachable next?**

Boundary is an experimental security layer for autonomous agents and MCP systems.

It models agents, tools, information, communication paths, derivations and security invariants to reason about the consequences of an action **before the full sequence occurs**.

The objective is not only to stop a prohibited final action.

The objective is to identify when an earlier, individually legitimate action creates a path toward a prohibited outcome.

---

## The problem

AI agents increasingly interact with powerful systems:

- MCP servers
- databases
- CRMs
- filesystems
- email
- messaging platforms
- SaaS APIs
- internal tools
- other autonomous agents

Traditional authorization is usually evaluated one operation at a time.

But an unsafe outcome may emerge from a sequence of individually legitimate operations.

For example:

```text
read_customer
      |
      v
read_support_notes
      |
      v
derive_sensitive_information
      |
      v
relay_to_another_agent
      |
      v
send_external
```

A conventional control may permit the early operations and only reject `send_external`.

By then, sensitive information may already have propagated through the agent system.

Boundary attempts to reason about the reachable future **before that happens**.

---

## Predictive reachability

Boundary evaluates how the state of an agent system changes when an action succeeds.

Conceptually:

```text
CURRENT STATE
     |
     | proposed action
     v
PREDICTED STATE
     |
     | reachability analysis
     v
FUTURE POSSIBILITIES
     |
     +------ safe
     |
     +------ sensitive
     |
     +------ prohibited
```

If a proposed action creates a path toward a configured prohibited state, Boundary can intervene before that path is executed.

This allows Boundary to reason about security properties that span multiple steps rather than evaluating only the current tool invocation.

---

## Enforcement decisions

Boundary currently produces three primary decisions:

```text
ALLOW
REQUIRE_APPROVAL
BLOCK
```

### ALLOW

The modeled action does not create a prohibited reachable state under the current security model.

```text
crm_read_customer
        |
        v
      ALLOW
```

### REQUIRE_APPROVAL

The current action may be legitimate by itself, but allowing it creates a security-relevant future that requires human authorization.

```text
crm_read_support_notes
        |
        v
REQUIRE_APPROVAL

Reason:
A prohibited information flow
becomes reachable downstream.
```

### BLOCK

The action violates the configured security model or cannot safely be authorized.

```text
send_external
      |
      v
    BLOCK
```

Boundary also follows deny-by-default behavior for unmodeled tools in the current alpha.

---

## Example

Consider an agent that can access customer information and communicate externally.

A traditional policy might allow:

```text
read_customer
read_support_notes
```

and block:

```text
send_sensitive_data_external
```

Boundary additionally reasons about whether allowing an earlier operation changes what becomes possible later.

For example:

```text
Step 1

read_customer
→ ALLOW


Step 2

read_support_notes
→ REQUIRE_APPROVAL

Reason:
Sensitive information can now be derived
and a path to an external sink becomes reachable.


Step 3

send_external
→ BLOCK
```

The important distinction is that Boundary can intervene at **Step 2**, before the prohibited final operation is requested.

---

## MCP architecture

Boundary can operate as a security gateway between an MCP client and an existing MCP server.

```text
+----------------------+
| AI Agent / MCP Client|
+----------+-----------+
           |
           v
+------------------------------+
|       Boundary Gateway       |
|                              |
|  Predictive reachability     |
|  Information-flow reasoning  |
|  Safety invariants           |
|  Human approval              |
|  Deny-by-default             |
|  Audit                       |
+--------------+---------------+
               |
               v
+------------------------------+
|     Existing MCP Server      |
+------------------------------+
```

The agent continues interacting through MCP.

Boundary evaluates modeled tool calls before forwarding them upstream.

---

## Multi-agent reasoning

Security becomes more difficult when multiple agents can communicate.

Information that is safe in one context may become dangerous after being transferred to another agent with different capabilities.

Boundary models communication paths as part of the reachable system state.

Conceptually:

```text
Agent A
  |
  | acquires sensitive information
  v
Agent A + sensitive information
  |
  | communication
  v
Agent B + sensitive information
  |
  | Agent B has external capability
  v
External sink
```

This allows security reasoning to extend beyond a single agent or a single tool call.

---

## Derived information

Sensitive information does not always need to be read directly.

Agents may derive new information from data that appears harmless when considered separately.

Boundary can model derivation rules such as:

```text
information_A
      +
information_B
      |
      v
derived_sensitive_information
```

The resulting information can then participate in subsequent reachability analysis.

---

## Safety invariants

Boundary security policies describe states that must not become reachable.

Examples include:

```text
Sensitive information must not reach an external sink.
```

or:

```text
Agent B must never possess both information X
and capability Y without approval.
```

Boundary evaluates proposed actions against these invariants and the modeled future state of the system.

---

## Human approval

Some actions are not inherently malicious but cross a security boundary.

Boundary can return:

```text
REQUIRE_APPROVAL
```

instead of immediately blocking them.

The current alpha supports action-bound, one-shot approval tokens with replay protection.

Conceptually:

```text
Agent action
     |
     v
Boundary
     |
     +---- ALLOW ----------> execute
     |
     +---- BLOCK ----------> reject
     |
     +---- REQUIRE_APPROVAL
                |
                v
             Human
                |
             approve
                |
                v
             execute
```

---

## Current capabilities

The current Boundary private alpha includes:

- predictive multi-step reachability analysis
- multi-agent information-flow modeling
- derived-information reasoning
- configurable safety invariants
- MCP tool discovery
- MCP proxy enforcement
- `ALLOW`
- `REQUIRE_APPROVAL`
- `BLOCK`
- action-bound approval tokens
- one-shot approval semantics
- replay protection
- deny-by-default handling for unmodeled tools
- audit logging
- YAML configuration
- command-line tooling

Boundary is under active development and these capabilities may change during the alpha.

---

## What Boundary is not

Boundary is not:

- an LLM prompt filter
- a replacement for authentication
- a replacement for authorization
- a malware scanner
- a generic MCP firewall based only on static allowlists
- a guarantee that an incompletely modeled system is safe

Boundary is intended to complement existing security controls by reasoning about **future reachable behavior**.

---

## Security model limitations

Boundary reasons about the world represented by its model.

Its decisions depend on the accuracy and completeness of:

- modeled agents
- tool capabilities
- information classes
- derivation rules
- communication paths
- external sinks
- safety invariants

An unmodeled capability can create a path that Boundary does not know exists.

The current alpha should therefore be treated as an experimental security system, not as a production security boundary.

---

## Current status

Boundary is currently in a:

**Controlled Private Alpha**

Current evaluation build:

```text
Boundary 0.0.1 alpha 2
Platform: Linux x86_64
Python: 3.14
Status: Experimental
```

The protected implementation is **not published in this repository**.

Selected evaluators receive a compiled Boundary runtime under separate evaluation terms.

The private source repository, research implementation and internal benchmark suite are not distributed.

---

## Why a controlled alpha?

Boundary is currently testing a fundamental research and product hypothesis:

> **Does predictive multi-step reachability provide a meaningful security property beyond conventional per-call authorization and runtime policy enforcement?**

We do not only want confirmation.

We actively want:

- criticism
- counterexamples
- adversarial scenarios
- integration failures
- false positives
- false negatives
- cases where conventional policy systems already solve the problem adequately

If the idea is wrong, we want to discover that early.

If it is useful, we want to understand precisely where.

---

## Who should test Boundary?

We are especially interested in engineers and teams working with:

- Model Context Protocol (MCP)
- autonomous AI agents
- multi-agent systems
- agent infrastructure
- AI security
- runtime authorization
- security gateways
- sensitive-data workflows
- enterprise AI systems

You do not need to deploy Boundary in production.

The alpha is specifically intended for controlled evaluation and technical feedback.

---

## Request Private Alpha access

If you would like to evaluate Boundary:

### Website

**https://boundarysec.com/#alpha**

### Email

**info@boundarysec.com**

Please tell us briefly:

- what you are building
- your agent/MCP stack
- how you currently secure tool access
- what scenario you would like to test

Selected testers receive the controlled evaluation runtime and onboarding instructions.

---

## Public repository

This repository exists for:

- public Boundary documentation
- architecture explanations
- alpha information
- research discussion
- project updates

It intentionally does **not** contain:

```text
Boundary private source code
Protected reachability implementation
Internal research code
Internal benchmark suite
Controlled runtime binaries
```

Please do not expect the executable Boundary implementation to appear in this repository during the controlled alpha.

---

## Responsible disclosure

If you discover a potential security vulnerability in Boundary, please **do not publish it as a public GitHub issue**.

Contact:

**info@boundarysec.com**

Include when possible:

- Boundary version
- environment
- MCP implementation
- expected behavior
- observed behavior
- reproduction steps

---

## Research discussion

Boundary is interested in a broader question:

> How should autonomous systems be secured when the security consequence of an action depends not only on what the action does now, but on what the action enables later?

We welcome technical discussion around:

- predictive authorization
- reachability analysis
- agent capability graphs
- information-flow security
- multi-agent containment
- MCP security
- human-in-the-loop enforcement

Technical disagreement is welcome.

---

## Roadmap

During the private alpha we are focused on:

```text
Real-world validation
        ↓
External adversarial testing
        ↓
MCP integration feedback
        ↓
Modeling improvements
        ↓
Runtime hardening
        ↓
Broader platform support
```

The roadmap will be driven primarily by evidence from real agent systems rather than feature count.

---

## Contact

Boundary Security

**Website:** https://boundarysec.com

**Email:** info@boundarysec.com

---

<p align="center">
  <strong>Boundary</strong><br>
  Predict the dangerous path before the agent takes it.
</p>

<p align="center">
  © 2026 Boundary Security. All rights reserved.
</p>
