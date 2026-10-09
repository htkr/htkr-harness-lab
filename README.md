# htkr-harness-lab

Experimental agent-harness lab for two workflows built on a shared runtime:

1. **Slides profile** — research -> evidence -> HTML prototype -> explicit human approval -> editable PPTX.
2. **Coding profile** — requirements/issues -> implementation -> verification -> independent review -> PR.

The target model providers are **Anthropic Claude API** and **OpenAI API**. The base harness is intentionally undecided at project start; the first spike compares **Pi** and **DeepSeek Harness**, with **Oh My Pi** kept as a coding-focused reference/optional profile.

> [!WARNING]
> This repository is **public**. Do not commit company names, internal URLs, credentials, proprietary PowerPoint templates, confidential prompts, production data, screenshots, generated decks based on internal data, or other non-public material.

## Design goals

- **Small core, separate profiles.** Share model routing, sessions, tools, compaction and telemetry; keep slides and coding workflow logic separate.
- **Provider-portable.** Workflows should select logical model roles such as `fast`, `reasoning` and `review`, not hard-code vendor model IDs everywhere.
- **Human gates where they matter.** Slides require approval before PPTX generation; protected SSOT changes require human-controlled PR approval.
- **Fast by default, traceable when factual.** The slides path prioritizes time-to-first-prototype while retaining claim/source traceability for web-researched facts.
- **Minimal governance.** Prefer GitHub-native enforcement (PRs, CODEOWNERS, rulesets) over custom local security mechanisms.
- **Measure changes.** Prompts, skills, orchestration, compaction and model choices should be compared with repeatable benchmarks.

## Planned architecture

```text
htkr-harness-lab/
├─ core/                 # shared runtime: models, tools, sessions, telemetry
├─ profiles/
│  ├─ slides/            # research, evidence, slide AST, HTML/PPTX renderers
│  └─ coding/            # skills, issue/worktree/review/PR workflow
├─ ssot/                 # small set of durable authoritative documents
├─ changes/              # agent-editable change proposals
├─ docs/
│  └─ adr/               # durable architecture decisions
├─ tests/
├─ benchmarks/
├─ .local/               # LOCAL ONLY: proprietary templates/config/data (ignored)
└─ artifacts/            # generated local outputs (ignored)
```

This is the intended shape, not a commitment to pre-create every directory. The repository should stay sparse until the foundation spike selects the base harness.

## Slides profile

Target workflow:

```text
request
  -> story / research questions
  -> parallel web research
  -> evidence pack
  -> semantic slide AST
  -> HTML/CSS prototype
  -> visual checks
  -> explicit user approval
  -> editable PPTX using a local template
  -> PPTX render/validation
```

The HTML prototype and PPTX should be rendered from the **same semantic slide representation**. HTML is a fast review surface; it is not the final source of truth for PowerPoint structure.

Real company templates must be supplied from an ignored local path, for example:

```text
.local/templates/company-template.pptx
```

Only synthetic/open fixtures may be committed.

## Coding profile

Target workflow:

```text
request / GitHub issue
  -> requirement interrogation / repository wayfinding
  -> change proposal or lightweight spec when needed
  -> isolated branch + worktree
  -> implementation
  -> tests / verification
  -> independent fresh-context review
  -> PR
  -> human or policy-controlled merge
```

The initial plan is to reuse the useful ideas from **Matt Pocock skills** and **pstack** without importing unnecessary framework-specific orchestration. Small composable skills are preferred over a monolithic agent mode.

## SSOT and protected paths

The current hypothesis is simpler than implementing a custom protected-path security layer inside the harness:

```text
ssot/
├─ constitution/         # highest-authority invariants
├─ architecture/         # architecture / ADR-linked authority
└─ product/              # durable product requirements

changes/                 # proposals and implementation planning
```

For protected paths, the harness should detect intent and route the change through a branch/PR, but the **actual enforcement belongs to GitHub** using CODEOWNERS plus branch protection/rulesets. This keeps the local harness from becoming its own security boundary.

OpenSpec is a candidate for complex change transactions, but it is not assumed to be mandatory for every change. A lightweight `changes/<id>/` convention should be evaluated first.

## Local setup and secrets

The implementation is not bootstrapped yet. Until Issue #1 selects the base harness, there is intentionally no fake install command.

When local development begins:

- keep API keys in environment variables or an ignored local secret file;
- keep proprietary templates/data under `.local/` or another ignored path;
- keep generated decks, logs and run outputs under `artifacts/`;
- never paste secrets or proprietary source material into GitHub Issues or committed fixtures.

Typical provider variables will likely include names such as:

```text
ANTHROPIC_API_KEY=...
OPENAI_API_KEY=...
```

Do **not** commit real values. A safe `.env.example` may be added later when the runtime configuration is finalized.

## Roadmap

The work is tracked in GitHub Issues. The first milestones are:

1. Compare Pi vs DeepSeek Harness on the same slides/coding mini-benchmarks.
2. Build the shared Claude/OpenAI runtime.
3. Implement slides research/evidence and HTML approval flow.
4. Implement editable PPTX generation using a local template.
5. Compose Matt Pocock/pstack-inspired coding skills.
6. Validate minimal SSOT governance and GitHub-enforced protected paths.
7. Automate Issue -> worktree -> independent review -> PR.
8. Add repeatable harness evaluations.

## Public-repository safety

Before `git add` or `git push`, check for:

- `.env` files and API keys/tokens;
- internal hostnames, repository URLs or ticket IDs;
- proprietary `.pptx`, `.pdf`, screenshots or brand assets;
- customer/company data in prompts, fixtures, logs or benchmark outputs;
- local run/session state that may contain prompt or source text.

If a secret is ever committed, removing the file in a later commit is not sufficient: rotate/revoke the secret first, then clean repository history as appropriate.

## Third-party code and licenses

The root `LICENSE` covers original code in this repository. External harnesses, skills and libraries keep their own licenses and notices.

Before vendoring or copying third-party source/skill content:

1. record the upstream project and exact version/commit;
2. verify license compatibility;
3. preserve required copyright/license notices;
4. prefer dependency/reference/reimplementation over copying when practical.

A third-party notices/provenance file will be added before source code is vendored.

## Status

**Experimental / pre-foundation.** The repository currently contains architecture decisions and implementation issues, not a stable harness API.

## License

Original code in this repository is licensed under the [MIT License](LICENSE), unless a file or third-party component states otherwise.
