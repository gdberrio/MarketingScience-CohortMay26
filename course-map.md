# May-26 Course Map

This map turns the March cohort into the May agentic cohort. The March notebooks
remain the source technical curriculum. May adds agentic labs, artifacts, and
skill/eval deliverables around those notebooks.

## Week 0: Setup And Agent Readiness

### Source Material

- `../cohort-Mar26/README.md`
- `../cohort-Mar26/pre-course/00_smoke_test.ipynb`
- `../cohort-Mar26/pre-course/notebook_00_explore_dataset.ipynb`
- `../cohort-Mar26/AGENTS.md`
- `../cohort-Mar26/CLAUDE.md`

### New May Layer

- Install and verify Python/Jupyter environment.
- Orient students to coding agents as tools that can read, edit, and run code.
- Explain project instructions (`AGENTS.md`, `CLAUDE.md`, or harness equivalent).
- Write a first checkpoint file after setup.
- Inspect one tiny skill and identify what failure it prevents.

### Deliverables

- Smoke test executed.
- Agent answers a constrained repo-orientation prompt.
- `artifacts/checkpoint-template.md` completed for setup status.

## Week 1: Measurement Foundations Plus Context Engineering

### Source Material

- `../cohort-Mar26/pre-course/reading_01_what_is_mmm.md`
- `../cohort-Mar26/pre-course/reading_03_causal_inference.md`
- `../cohort-Mar26/week-1/session-1/session_01_measurement_landscape.ipynb`
- `../cohort-Mar26/week-1/session-2/session_02_data_prep_eda.ipynb`
- `../cohort-Mar26/week-1/offline/notebook_01_eda.ipynb`
- `../LightingSessions/Context-rot-Claude-Code-Marketing-Analytics/session-plan.md`

### New May Layer

- Teach context rot and context engineering before long analytical sessions.
- Add agentic EDA replay after manual EDA.
- Require students to verify agent output against column roles, missingness,
  time grain, correlations, and visual checks.
- Store EDA conclusions in an artifact instead of leaving them only in chat.

### Deliverables

- EDA notebook or March notebook run.
- `artifacts/eda-findings-template.md` completed.
- `labs/week-1/agentic-eda-lab.md` completed.

## Week 2: MMM From Scratch Plus Skill-Guided Iteration

### Source Material

- `../cohort-Mar26/week-2/offline/notebook_02_adstock_saturation.ipynb`
- `../cohort-Mar26/week-2/session-3/session_03_ols_mmm.ipynb`
- `../cohort-Mar26/week-2/offline/notebook_03_pymc_basics.ipynb`
- `../cohort-Mar26/week-2/session-4/session_04_bayesian_mmm.ipynb`
- `../LightingSessions/MMM-30mins-Claude-Code/session-plan.md`
- `../LightingSessions/Bayesian-v-Frequencist-MMM/session-plan/session-plan.md`

### New May Layer

- Show a naive agent workflow failing on variable selection or diagnostics.
- Use a variable-mapping skill to create a model-ready role table.
- Compare manual OLS/Bayesian modeling with agent-assisted iteration.
- Capture model decisions, rejected specs, diagnostics, and caveats.

### Deliverables

- Manual model notebook.
- `artifacts/variable-map-template.md` completed.
- `artifacts/model-iteration-log-template.md` completed.
- `labs/week-2/skill-guided-mmm-lab.md` completed.

## Week 3: Meridian, Attribution, And Framework-Specific Skills

### Source Material

- `../cohort-Mar26/week-3/session-5/session_05_meridian.ipynb`
- `../cohort-Mar26/week-3/offline/notebook_04_meridian_setup.ipynb`
- `../cohort-Mar26/week-3/offline/notebook_06_meridian_post_modeling.ipynb`
- `../cohort-Mar26/week-3/session-6/session_06_attribution_byod.ipynb`
- `../cohort-Mar26/.claude/skills/meridian-model/SKILL.md`
- `~/1-Projects/MarketingSciencePlugin/skills/meridian-setup/SKILL.md`
- `~/1-Projects/MarketingSciencePlugin/skills/meridian-fit/SKILL.md`
- `~/1-Projects/MarketingSciencePlugin/skills/meridian-diagnostics/SKILL.md`

### New May Layer

- Treat Meridian setup as an artifact-driven workflow.
- Separate model specification, fit record, and diagnostics report.
- Use framework-specific skills for setup pitfalls such as dimensions, media
  vs media spend, controls, NaN handling, and population.
- Keep attribution as a measurement method, but connect it to agent validation:
  agents can compute Shapley or Markov outputs, but students must interpret
  limits.

### Deliverables

- Meridian model spec or dry-run spec.
- Meridian fit record if fitting is feasible.
- Diagnostics review with explicit caveats.
- `labs/week-3/meridian-skill-lab.md` completed.

## Week 4: Experiments, Calibration, Evals, And Capstone

### Source Material

- `../cohort-Mar26/week-4/session-7/session_07_experimentation_geolift.ipynb`
- `../cohort-Mar26/week-4/offline/notebook_06_power_analysis.ipynb`
- `../cohort-Mar26/week-4/offline/reading_06_calibrating_mmm.md`
- `../cohort-Mar26/week-4/session-8/session_08_prior_calibration.ipynb`
- `../cohort-Mar26/capstone/reading_07_capstone_guide.md`
- `../cohort-Mar26/capstone/capstone_template.ipynb`
- `../LightingSessions/How-Setup-Experiments-Claude-Code/session-plan.md`

### New May Layer

- Compare manual geo-experiment design prompts with a reusable skill workflow.
- Teach calibration handoff as the bridge from experiments to MMM.
- Add behavior evals for skills and agent workflows.
- Require capstone teams to create or improve one reusable workflow asset.

### Deliverables

- Lift-test design brief or experiment analysis report.
- Calibration handoff.
- One skill or skill patch.
- One behavior eval case.
- Updated capstone package.

## Cross-Course Assessment

Students are assessed on both measurement quality and workflow quality.

Measurement quality:

- causal claim is explicit
- variables are correctly classified
- model limitations are stated
- diagnostics and uncertainty are interpreted correctly
- recommendations match evidence strength

Workflow quality:

- project instructions are compact
- checkpoints preserve key decisions
- artifacts are complete and inspectable
- skill boundary is clear
- eval case targets a real high-risk failure

