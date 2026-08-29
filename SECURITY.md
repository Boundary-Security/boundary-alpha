# Security Policy

Boundary is experimental security software in controlled private alpha and external validation.

It is not currently intended to serve as a production security boundary.

## Reporting a vulnerability

Please do not disclose suspected Boundary vulnerabilities through public GitHub issues.

Report security concerns privately to:

**info@boundarysec.com**

When possible, include:

- affected Boundary version
- operating environment
- MCP implementation involved
- expected behavior
- observed behavior
- reproduction steps
- potential security impact

We will review reports and coordinate disclosure when appropriate.

## Current security status

Boundary evaluates the system represented by its configuration and runtime model.

Security conclusions therefore depend on the accuracy and completeness of modeled agents, capabilities, information, derivations, communication paths, external sinks and policies.

An unmodeled capability or transition may create security-relevant behavior that Boundary cannot reason about.

The predictive production behavior validated in the current controlled alpha focuses specifically on modeled sensitive external disclosure.

Generic predictive enforcement for arbitrary security invariants has not been established.

Current research includes known modeling limitations around:

- artifact-specific acquisition
- artifact-specific external-send semantics
- changing communication topology
- arbitrary future acquisition prediction

Boundary may intervene conservatively when a risky future is reachable even if the intended workflow would remain benign. Such unnecessary interventions and false positives are part of the current external-validation program.

## External testing

External testers are encouraged to report:

- false positives
- false negatives
- modeling gaps
- unexpected approval requirements
- unexpectedly allowed behavior
- MCP integration failures
- configuration ambiguities
- ways the security model can be bypassed or misrepresented

Do not use real secrets, production credentials or destructive production systems when testing Boundary.

Controlled adversarial testing using synthetic or disposable data is welcome.

## Responsible disclosure

Security findings should be reported privately to:

**info@boundarysec.com**

Please preserve reproduction information where possible.

Boundary welcomes critical security research and counterexamples.
