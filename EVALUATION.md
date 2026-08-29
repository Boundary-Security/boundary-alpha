# Boundary Controlled Alpha Evaluation

Boundary is recruiting selected external evaluators for controlled technical validation.

The protected Boundary implementation and internal research repository are not contained in this public repository.

## Current evaluation package

- Boundary 0.0.1a2
- Python >= 3.12
- MCP >= 2.1,<3
- Experimental / non-production

## Current evidence

Boundary has completed controlled internal validation using real MCP processes.

The validated execution path includes:

```text
real MCP client
      |
      v
Boundary MCP proxy
      |
      v
dynamic mirrored tools
      |
      v
Boundary enforcement
      |
      v
real upstream MCP server
```

Controlled testing has demonstrated:

- allowed upstream execution
- dynamic upstream tool discovery and mirroring
- deny-by-default behavior for unmodeled tools
- enforcement before blocked upstream side effects
- approval gating before upstream execution
- predictive intervention for modeled sensitive external disclosure

The current internal regression suite contains **208 passing tests**.

The controlled workflow has also been reproduced internally from a fresh checkout and fresh virtual environment.

These results are controlled internal evidence. They are **not** presented as independent external validation.

## What we want to learn

We want to determine whether predictive reachability provides a useful security property beyond conventional per-call authorization and runtime policy enforcement.

We are specifically interested in:

- external reproduction
- independently selected MCP workflows
- benign workflows that trigger unnecessary intervention
- dangerous workflows Boundary fails to identify
- modeling gaps
- MCP integration problems
- usability problems
- comparisons with existing authorization and policy systems

Negative evidence is useful.

## External validation process

### Phase A — Reproduction

The evaluator receives a frozen Boundary checkpoint and reproduction instructions. The first workflow is executed without modifying the frozen scenario. Unexpected results and setup failures must be preserved.

### Phase B — Independent challenge

After Phase A, the evaluator selects a small MCP or agent-tool workflow.

Before Boundary is executed, the evaluator records:

- legitimate workflow objective
- benign behavior
- unsafe behavior
- whether intervention is expected
- approximate expected intervention location when applicable

Those expectations are frozen before evaluation.

## Current limitations

Boundary reasons only about the system represented by its model.

Current research limitations include incomplete support for:

- artifact-specific acquisition
- artifact-specific external-send semantics
- dynamic communication topology
- arbitrary future acquisition prediction
- generic predictive coverage across arbitrary invariants

Current validated predictive production behavior focuses specifically on modeled sensitive external disclosure.

The alpha may also intervene conservatively when a dangerous future is reachable even when the intended workflow is benign. False-positive behavior is an explicit validation target.

## Safety

Do not use production credentials, real customer secrets, private production data or destructive production tools during evaluation. Use synthetic or disposable test data.

Boundary is not currently intended to serve as a production security boundary.

## Request access

**Website:** https://boundarysec.com/#alpha

**Email:** info@boundarysec.com

Please include what you are building, your agent or MCP stack, how tool access is currently secured and the workflow you would like to evaluate.

## Evidence policy

Boundary does not treat distribution of an evaluation package as validation.

External evidence is recorded only after an external tester returns reproduction or independently supplied workflow results.

Failures, false positives, false negatives, setup failures and modeling gaps are preserved as part of the evaluation record.
