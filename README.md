## How to read the SCF-PCI scenario datasets

This repository contains ten SCF-PCI scenario datasets. Each scenario has three linked artefacts:

1. a scenario narrative, which explains the operational story;
2. a scenario graph, which shows the situation-action structure;
3. an Excel workbook, which records the state-indexed PCI DSS valuation results.

The narrative and graph should be read first. They explain what each state means and how the scenario moves from one state to another. The workbook then shows how SCF-PCI evaluates the reached states, applies requirement-check predicates, and computes the path-level results reported in the paper.

Each workbook follows the same structure and should be read from Sheet 01 to Sheet 06.

### Sheet 01: State catalogue

This sheet defines the state space for the scenario. Each row corresponds to a situation in the scenario graph.

Use this sheet to understand:

- the state identifier, such as `S0`, `S1`, or `S10`;
- the meaning of the state;
- the threat or operational condition represented at that state;
- the items present in the state;
- the licensed conditions attached to the state;
- the PCI evaluation context;
- the outgoing transitions to later states.

The key SCF-PCI input is the state snapshot:

`pi(s) = <O_s, Gamma_s>`

where `O_s` is the set of represented items and `Gamma_s` is the set of licensed conditions. SCF-PCI only evaluates facts that are represented in the snapshot or admitted through the PCI evaluation context. It does not infer unrepresented organisational facts, assessor judgement, live telemetry, or formal PCI DSS scope determinations.

### Sheet 02: Applicability matrix

This sheet shows whether each instantiated PCI DSS requirement check applies in each state.

The main column is usually `Appr` or an equivalent applicability column:

- `1` means the check is applicable in that state;
- `0` means the check is not applicable in that state.

Applicability is evaluated before satisfaction. A non-applicable check is not a failed control. It means the model-local state does not license the operational conditions required for that specific check.

### Sheet 03: Valuation matrix

This sheet records the check-level valuation assigned to each state inside the PCI valuation domain.

The notation is:

- `S` = `SatisfiedInState`
- `V` = `ViolatedInState`
- `NA` = `NotApplicable`

These are the only three check-level valuation outcomes used by SCF-PCI.

A state outside `D_PCI` is not assigned a check-level valuation. This is different from `NA`. `NA` means the state is inside the PCI valuation domain but the specific check does not apply. A state outside `D_PCI` is excluded before check-level valuation.

### Sheet 04: Path metrics

This sheet gives the path-level results for the scenario.

Each row corresponds to a finite path through the scenario graph. The sheet reports:

- the path name;
- the sequence of states in the path;
- the terminal or bounded endpoint;
- the path length;
- `|V_end|`, the number of violated checks visible at the endpoint;
- `|V_path|`, the number of violated checks observed anywhere along the path;
- `|V_miss|`, the number of violations missed by the endpoint-only view;
- the earliest state where each violated check appears;
- an interpretation of the path-level result.

The core relation is:

`V_miss = V_path \ V_end`

This means that `V_miss` contains violations preserved by the state-indexed trace but not visible at the endpoint.

### Sheet 05: Reference checks

This sheet provides selected hand-checkable examples used to confirm that the executable output follows the SCF-PCI valuation semantics.

These rows compare expected outputs with the OPA/Rego output for representative cases, including:

- `NotApplicable`
- `SatisfiedInState`
- `ViolatedInState`
- domain-gating examples where a state is outside `D_PCI`

These are conformance checks on the executable model. They are not PCI DSS audit findings.

### Sheet 06: Sensitivity checks

This sheet records controlled input changes used to test whether valuation changes only when the relevant model-local input changes.

For example, a sensitivity row may remove PCI context, remove an applicability condition, or change satisfaction evidence. These rows are diagnostic checks. They are not additional scenario paths.

Where PCI context is withdrawn, the correct interpretation is:

`Outside D_PCI`

or:

`Excluded before check-level valuation`

This must not be read as a fourth valuation outcome. SCF-PCI has only three check-level outcomes inside `D_PCI`: `NotApplicable`, `SatisfiedInState`, and `ViolatedInState`.

## Reading order for reviewers

For each scenario, reviewers should read the files in this order:

1. read the scenario narrative;
2. inspect the scenario graph;
3. open the workbook;
4. read Sheet 01 to understand the states and transitions;
5. read Sheet 02 to see which checks become applicable;
6. read Sheet 03 to see the state-level valuations;
7. read Sheet 04 to verify the path-level metrics;
8. read Sheets 05 and 06 to inspect the reference and sensitivity checks.

The graph defines the transition structure. The narrative explains the scenario meaning. The workbook documents the state snapshots, applicability predicates, satisfaction outcomes, state-indexed valuations, and path-level retention metrics.
