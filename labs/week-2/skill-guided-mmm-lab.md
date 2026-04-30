# Week 2 Lab: Skill-Guided MMM Iteration

## Goal

Use a skill-guided workflow to map variables and iterate on an MMM while keeping
the model decisions auditable.

## Source Notebooks

Use:

```text
../cohort-Mar26/week-2/session-3/session_03_ols_mmm.ipynb
../cohort-Mar26/week-2/session-4/session_04_bayesian_mmm.ipynb
```

## Prompt

```text
Create a variable map for the MMM workshop data before fitting a model.
Use the data dictionary and config. Separate KPI, owned media spend, owned media
volume, controls, competitor variables, exclusions, and unresolved variables.
Flag aggregate/component duplication and variables that could be mediators or
outcome leakage.
```

After reviewing the variable map:

```text
Using the approved variable map, build a first MMM specification and log each
iteration. For every model, report diagnostics, coefficient-sign issues,
multicollinearity, and why the spec was kept or rejected. Save the decisions in
a model iteration log.
```

## Student Verification

Complete:

- `artifacts/variable-map-template.md`
- `artifacts/model-iteration-log-template.md`

Then answer:

1. Which variables did the agent initially misclassify or nearly misclassify?
2. Which diagnostic mattered most for rejecting a model?
3. Did the final recommendation overstate what the model can identify?
4. What failure from this lab should become an eval case?

