# SeenRelay Cost Prescreen

A local-only GitHub Action that checks a repository for repeated expensive read-only work before you integrate SeenRelay.

It is deliberately conservative:

- runs a static local prescreen only;
- uploads no repository source to SeenRelay;
- does not contact the SeenRelay service;
- does not modify the scanned project;
- does not enable reuse or suppress any authoritative call;
- does not return a `USE` verdict;
- prefers stronger local, source-native and provider-native controls first.

## Quick start

```yaml
name: SeenRelay cost prescreen

on:
  workflow_dispatch:

jobs:
  prescreen:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Prescreen repeated expensive read-only work
        id: seenrelay
        uses: ovladon/seenrelay-cost-prescreen@v1
```

To scan a subdirectory:

```yaml
- uses: ovladon/seenrelay-cost-prescreen@v1
  with:
    path: ./apps/agent
```

## Outputs

- `static_status` — static local prescreen state.
- `candidate_files` — number of files that matched prescreen heuristics.
- `decision` — next bounded decision from the non-mutating adoption plan.
- `report_path` — local JSON scan report path for later steps in the same job.
- `plan_path` — local JSON adoption-plan path for later steps in the same job.

A candidate is not proof of savings. It is only a reason to measure the workload properly.

## What happens next

If the prescreen finds a plausible candidate, measure it in shadow first: preserve every authoritative call, keep stronger native controls enabled, and compare real recurrence, safety and economics before any reuse policy is considered.

Valid prescreen outcomes include **REJECT BEFORE COLLECTION**, **MEASURE IN SHADOW**, and **INSUFFICIENT EVIDENCE**.

SeenRelay's later measured workload verdict remains **USE / DO NOT USE / INSUFFICIENT EVIDENCE**. This Action itself cannot issue a `USE` verdict.

## Privacy and safety boundary

This Action executes the published SeenRelay CLI locally on the GitHub runner. It does not send repository source to SeenRelay and does not make hosted CHECK/OBSERVE calls.

Do not interpret a static candidate as permission to suppress mutating, destructive, safety-critical or semantically independent work.

## SeenRelay

- Product and documentation: https://seenrelay.com
- Main repository: https://github.com/ovladon/seenrelay
- Trust posture: https://seenrelay.com/trust
- Public workload-audit intake: https://github.com/ovladon/seenrelay/issues/new?template=audit-my-workload.yml

The Action currently runs the verified public SeenRelay client `0.2.30`.
