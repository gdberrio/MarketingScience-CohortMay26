# Advanced Marketing Measurement with Coding Agents (Cohort May-26)

This cohort teaches marketing measurement and the agentic workflows that make
the work repeatable. Students build the core methods manually, then learn to
encode the judgment into reusable skills, artifacts, validation checklists, and
eval cases.

The course builds on the March 2026 bootcamp material, but the May version is
not "MMM plus an AI bonus." The agentic workflow layer is part of every week.

## Course Promise

By the end of the cohort, students can:

- build and critique MMM, attribution, and experiment workflows
- use coding agents to accelerate analysis without outsourcing judgment
- structure long agent sessions with compact instructions, checkpoints, and
  phase separation
- create reusable skill files for repeated marketing science workflows
- define artifact contracts that make agent output inspectable
- write lightweight behavior eval cases for high-risk skill failures

## Curriculum Shape

| Stage | Measurement Focus | Agentic Workflow Focus | Core Deliverable |
|---|---|---|---|
| Week 0 | Environment and repository setup | Agent readiness, project instructions, checkpoints | Smoke test plus first checkpoint |
| Week 1 | Measurement landscape, DAGs, EDA | Context engineering and agentic EDA verification | EDA findings artifact |
| Week 2 | Adstock, saturation, OLS, Bayesian MMM | Skill-guided variable mapping and diagnostics | Variable map plus model iteration log |
| Week 3 | Meridian and attribution | Framework-specific skills and artifact contracts | Meridian model spec or fit record |
| Week 4 | Experiments, calibration, capstone | Behavior evals and reusable workflow assets | Skill or skill patch plus eval case |

## Relationship To March Cohort

The March course remains the technical source curriculum:

- `../cohort-Mar26/pre-course/`
- `../cohort-Mar26/week-1/` through `../cohort-Mar26/week-4/`
- `../cohort-Mar26/capstone/`
- `../cohort-Mar26/utils/`
- `../cohort-Mar26/data/`

May uses that material as the measurement spine and adds:

- agentic replay labs
- artifact templates
- skill-building exercises
- validation checklists
- behavior eval examples
- capstone requirements for reusable workflows

## Repository Layout

```text
cohort-May26/
├── README.md
├── AGENTS.md
├── course-map.md
├── agentic-workflow-layer.md
├── REVIEW_AGENTIC_COURSE_UPDATE.md
├── artifacts/
├── evals/
├── labs/
└── references/
```

## Working With The Skill Pack

The companion skill pack lives locally at:

```text
~/1-Projects/MarketingSciencePlugin
```

Use it as a teaching artifact and optional workflow dependency. The core
progression is:

1. Inspect a course-specific skill.
2. Use a workflow skill on course data.
3. Critique the output against a validation checklist.
4. Adapt or create a skill.
5. Write one behavior eval case for a high-risk failure.

## Validation

There is no single build system yet. Validate changes by:

- reading changed Markdown end to end
- running any referenced notebooks from the March cohort when notebook content is
  copied or modified
- checking eval YAML structure before using it in a runner
- keeping generated outputs out of the course source unless they are deliberate
  examples

