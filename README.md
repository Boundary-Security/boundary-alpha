<p align="center">
  <img src="assets/boundary-logo.png" alt="Boundary Security" width="150">
</p>

<h1 align="center">Boundary</h1>

<p align="center"><strong>Predictive security enforcement for autonomous AI agents and MCP systems.</strong></p>
<p align="center">Stop dangerous agent behavior before the dangerous action is requested.</p>

<p align="center">
  <a href="https://boundarysec.com">Website</a> ·
  <a href="https://boundarysec.com/#alpha">Request Private Alpha</a> ·
  <a href="mailto:info@boundarysec.com">Contact</a>
</p>

---

## What is Boundary?

Most authorization systems ask:

> **Is this action allowed right now?**

Boundary asks an additional question:

> **If this action is allowed now, what dangerous future states become reachable next?**

Boundary is an experimental security layer for autonomous agents and Model Context Protocol (MCP) systems.

It models agents, capabilities, information, derivations, communication paths and security policies to reason about the consequences of an action before a complete sequence occurs.

The goal is not only to stop a prohibited final action. Boundary investigates whether an earlier, individually legitimate action changes the modeled state so that a prohibited outcome becomes reachable later.

## The problem

AI agents increasingly interact with MCP servers, databases, CRMs, filesystems, email, messaging platforms, SaaS APIs, internal tools and other autonomous agents.

Traditional authorization is commonly evaluated one operation at a time. But an unsafe outcome can emerge from a sequence of individually legitimate operations:

```text
read information A
        |
        v
read information B
        |
        v
derive sensitive information
        |
        v
relay to another agent
        |
        v
external sink
```

A conventional control may reject only the terminal operation. Boundary investigates whether the dangerous path can be identified earlier.

## Predictive reachability

Boundary evaluates how the modeled state changes if a proposed action succeeds.

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
     +------ security-relevant
     |
     +------ prohibited
```

When the current production model finds that a proposed action makes modeled sensitive external disclosure reachable, Boundary can intervene before the terminal disclosure action is requested.

The present controlled validation is specifically scoped to this modeled sensitive-external-disclosure path. It should not be interpreted as generic prediction of every possible future security invariant.

## Enforcement decisions

Boundary currently produces:

```text
ALLOW
REQUIRE_APPROVAL
BLOCK
```

**ALLOW** — the modeled action is permitted under the current state and configured security model.

**REQUIRE_APPROVAL** — the current action can be safe by itself while making a modeled sensitive external disclosure reachable downstream. Boundary can gate that action before forwarding it upstream.

**BLOCK** — the action violates the configured model, is invalid in the modeled state, or is denied by current enforcement policy.

Unmodeled MCP tools are handled deny-by-default in the current alpha.

## Example

Consider a modeled workflow in which separate pieces of public information can be combined into sensitive derived information, and another agent has a route to an external sink.

An early read can be individually legitimate. After additional information becomes available, however, the modeled state may make future sensitive external disclosure reachable.

Boundary can then return `REQUIRE_APPROVAL` before the terminal disclosure action is requested.

This is the predictive behavior currently being evaluated. It is not a claim of generic prediction across arbitrary workflows or invariants.

## MCP architecture

Boundary can operate as a security proxy between a real MCP client and an existing MCP server.

```text
AI Agent / MCP Client
          |
          v
+-----------------------------+
|     Boundary MCP Proxy      |
| Dynamic tool mirroring      |
| Predictive evaluation       |
| Information-flow reasoning  |
| Human approval              |
| Deny-by-default             |
| Audit                       |
+-------------+---------------+
              |
              v
      Upstream MCP Server
```

Controlled real-tool validation has exercised:

```text
real MCP ClientSession
        |
        v
Boundary proxy process
        |
        v
dynamically mirrored MCP tools
        |
        v
Boundary policy evaluation
        |
        v
real upstream MCP process
```

Allowed operations reached the upstream MCP server. Blocked or approval-gated operations were withheld before the corresponding upstream side effect occurred.

## Multi-agent and derived-information reasoning

Boundary models communication paths between agents and can model derivation rules where multiple inputs produce new sensitive information.

```text
information_A + information_B
              |
              v
   derived_sensitive_information
              |
              v
         another agent
              |
              v
        external sink
```

The derived information can participate in subsequent modeled reachability analysis.

## Safety policies

The primary production policy exercised in the current controlled predictive validation is:

```text
Sensitive information must not reach an external sink.
```

The research architecture explores broader state-based security reasoning, but generic predictive enforcement for arbitrary invariants has not been established.

## Human approval

The current alpha supports action-bound, one-shot approval tokens with replay protection.

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

## Current capabilities

The current controlled alpha includes:

- predictive reachability for modeled sensitive external disclosure
- multi-agent information-flow modeling
- derived-information reasoning
- configurable security policy modeling
- real MCP client/proxy/upstream operation
- dynamic MCP tool discovery and mirroring
- enforcement before upstream execution
- `ALLOW`, `REQUIRE_APPROVAL`, `BLOCK`
- action-bound, one-shot approval tokens with replay protection
- deny-by-default handling for unmodeled tools
- audit logging
- YAML configuration and semantic validation
- command-line tooling
- controlled real-tool MCP validation
- clean-room reproduction procedures

## Controlled validation evidence

Boundary has completed controlled internal validation using real MCP processes.

Current evidence includes:

- real MCP client → Boundary proxy → real upstream MCP execution
- dynamic discovery and mirroring of upstream tools
- successful execution of allowed upstream operations
- unmodeled-tool denial before upstream execution
- enforcement before blocked upstream side effects
- approval gating before upstream execution
- predictive intervention when a currently safe action made modeled sensitive external disclosure reachable downstream
- reproduction from a fresh checkout and fresh virtual environment
- an internal regression suite currently containing **208 passing tests**

These are **controlled internal validation results**. They are not presented as independent external validation.

External reproduction and independently selected workflows are the next evidence targets.

## What Boundary is not

Boundary is not an LLM prompt filter, a replacement for authentication or conventional authorization, a malware scanner, a static MCP allowlist firewall, proof that an incompletely modeled system is safe, or a production-ready security boundary.

Boundary is intended to complement existing controls by reasoning about modeled future reachable behavior.

## Security model limitations

Boundary reasons about the world represented by its model. Decisions depend on the accuracy and completeness of modeled agents, capabilities, information classes, derivation rules, communication paths, external sinks and security policies.

Current research limitations include incomplete support for:

- artifact-specific acquisition
- artifact-specific external-send semantics
- changing communication topology
- arbitrary future acquisition prediction
- generic predictive coverage across arbitrary invariants

Boundary may also intervene conservatively when a dangerous future is reachable even if the intended workflow would remain benign. Measuring false positives and unnecessary interventions is an explicit external-validation objective.

The current alpha is experimental security software and should not be treated as a production security boundary.

## Current status

**Controlled Private Alpha / External Validation**

```text
Boundary: 0.0.1a2
Python: >= 3.12
MCP: >= 2.1,<3
Status: Experimental / non-production
```

The protected Boundary implementation is **not published in this repository**.

Controlled real-tool testing and internal clean-room reproduction have been completed. External reproduction by testers outside the Boundary development process is the next validation milestone.

The private source repository and internal research/benchmark material remain non-public.

## Why a controlled alpha?

Boundary is testing:

> **Does predictive multi-step reachability provide a meaningful security property beyond conventional per-call authorization and runtime policy enforcement?**

We actively want criticism, counterexamples, adversarial scenarios, integration failures, false positives, false negatives, modeling gaps and cases where conventional policy systems already solve the problem adequately.

## Who should test Boundary?

We are looking for engineers and researchers working with MCP, autonomous AI agents, multi-agent systems, agent infrastructure, AI security, runtime authorization, security gateways, sensitive-data workflows and enterprise AI systems.

The most useful testers are willing to reproduce the current controlled result and then challenge Boundary with workflows we did not design.

## External validation process

### Phase A — Reproduction

A tester receives a frozen Boundary checkpoint and reproduction instructions. The initial scenario is executed without changing the frozen workflow. Differences and failures are preserved.

### Phase B — Independent challenge

After reproduction, the tester selects a small MCP or agent-tool workflow independently. Before running Boundary, the tester records the legitimate objective, benign behavior, unsafe behavior and expected intervention behavior.

Those expectations are frozen before evaluation.

## Request Private Alpha access

Boundary is recruiting external technical evaluators.

**Website:** https://boundarysec.com/#alpha

**Email:** info@boundarysec.com

Please tell us briefly what you are building, your agent/MCP stack, how you currently secure tool access, whether you can reproduce a Python/MCP test environment, and what independent workflow you may be interested in testing.

Selected testers receive private evaluation access and onboarding instructions.

## Public repository

This repository contains public Boundary documentation, architecture explanations, alpha information, research discussion, project updates and external-validation information.

It intentionally does **not** contain the protected Boundary source implementation, private research code or internal benchmark suite.

## Responsible disclosure

Do not publish suspected Boundary vulnerabilities as public GitHub issues.

Report privately to **info@boundarysec.com** with the Boundary version, environment, MCP implementation, expected and observed behavior, reproduction steps and potential impact when available.

## Research discussion

Boundary is interested in a broader question:

> How should autonomous systems be secured when the security consequence of an action depends not only on what the action does now, but on what the action enables later?

Technical disagreement and counterexamples are welcome.

## Roadmap

```text
Controlled real-tool validation
        |
        v
Internal clean-room reproduction
        |
        v
External reproduction
        |
        v
Independently selected workflows
        |
        v
False-positive / false-negative analysis
        |
        v
Modeling improvements
        |
        v
Runtime hardening
        |
        v
Broader platform evaluation
```

The next major milestone is external evidence from testers operating outside the Boundary development process.

## Contact

Boundary Security

**Website:** https://boundarysec.com

**Email:** info@boundarysec.com

---

<p align="center">
  <strong>Boundary</strong><br>
  Predict the dangerous path before the agent takes it.
</p>

<p align="center">© 2026 Boundary Security. All rights reserved.</p>
