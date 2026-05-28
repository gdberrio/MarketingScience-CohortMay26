# Reading 7: LLM Evals For Agentic Marketing Measurement

**Estimated reading time: ~45 minutes**

**Class focus:** this reading prepares you to write, score, and interpret small
behavior evals for agentic marketing measurement workflows.

---

## Why Evals Belong In This Course

By Week 4, you have seen the pattern:

1. Learn the measurement method manually.
2. Ask an agent to help execute or replay the workflow.
3. Check the output.
4. Preserve decisions in artifacts.
5. Encode repeatable judgment in skills.

Evals are the next step. They answer a simple question:

> Can we trust this agentic workflow to preserve the marketing measurement
> judgment that matters?

An eval is not a vibe check, a leaderboard, or a generic "is the answer good?"
question. In this course, an eval is a compact test of a severe failure mode:

- Did the agent classify competitor spend as owned media?
- Did it include both an aggregate channel and its component channels?
- Did it turn a noisy geo-lift result into an overconfident MMM prior?
- Did it recommend budget changes without uncertainty or diagnostics?
- Did it keep going when a required artifact or input was missing?

The goal is not to prove that an agent is "smart." The goal is to catch the
mistakes that would make the analysis unsafe, misleading, or hard to reproduce.

---

## What The Course Already Gives You

The May cohort already has the pieces needed for lightweight behavior evals:

| Course asset | What it contributes |
|---|---|
| `agentic-workflow-layer.md` | Defines skills, artifacts, validation checklists, and behavior evals. |
| `skills/course-examples/` | Gives small, inspectable skills for MMM variable mapping, Meridian setup, lift design, and calibration. |
| `skills/skill-readiness-checklist.md` | Defines what a course-ready skill must include. |
| `references/how-to-write-a-skill.md` | Shows how to turn repeated judgment into a skill and add one behavior eval. |
| `artifacts/agent-output-validation-checklist.md` | Gives students a manual quality gate after agent-generated analysis. |
| `labs/week-4/evals-and-calibration-lab.md` | Asks students to write an eval case for a skill. |
| `evals/skills/mmm-variable-mapping/cases.yaml` | Provides a first YAML example of a behavior eval case. |

That foundation is strong. The missing piece is the eval mental model:

- what level of workflow you are testing
- which scorer belongs to which kind of claim
- how to record a run so it can be reproduced
- how many times to rerun a stochastic workflow
- how to interpret small eval samples without overclaiming
- when an LLM judge is useful and when it is the wrong tool

This reading fills that gap.

---

## The Eval Ladder

Not every eval tests the same thing. Before writing a case, name the unit under
test.

| Level | Unit under test | Example question | Good scorers |
|---|---|---|---|
| Prompt eval | One prompt on one input | Does the prompt surface variable-role mistakes? | Regex checks, structured output checks, human review |
| Skill eval | Skill plus prompt plus workspace | Does `mmm-variable-mapping` create the right role table and stop before modeling? | Artifact checks, schema checks, rubric checks |
| Artifact eval | A durable output | Does the Calibration Handoff preserve estimand, uncertainty, time period, population, and transferability limits? | Required-section checks, forbidden-claim checks, human review |
| Harness eval | Same task across agents or tools | Do Codex, Claude Code, and another harness reach comparable conclusions from the same prompt and files? | Paired pass/fail, trace review, artifact comparison |
| Narrative eval | Final claim or teaching story | Does the recommendation match the evidence strength? | Human review, rubric judge, numeric tolerance checks |

For this course, most student evals should be skill or artifact evals. Those are
close enough to the capstone work to be useful and small enough to write in an
afternoon.

---

## The Anatomy Of A Behavior Eval

A minimal behavior eval has five parts:

```yaml
cases:
  - id: short-readable-id
    skill: skill-name
    prompt: |
      The exact task the agent will receive.
    expected:
      - Behavior that must happen.
      - Artifact or caveat that must be present.
    disallowed:
      - Behavior that must not happen.
      - Unsupported claim that must not appear.
```

Good eval cases are specific. They name a failure you have actually seen or can
realistically expect.

Weak eval:

```yaml
expected:
  - Gives a good answer about MMM.
```

Better eval:

```yaml
expected:
  - Treats Brand_B_ATL_Spends as competitor/context, not owned media.
  - Flags Meta_Spends_Agg as a duplicate of Meta1_Spends and Meta2_Spends.
  - Excludes Sales_Revenue_Channel1 as outcome leakage.
disallowed:
  - Uses every column containing Spend as owned media.
  - Includes aggregate and component Meta spend in the same first-pass model.
```

The better version can be scored. A reviewer can look at the output and decide
whether the required and forbidden behaviors happened.

---

## Example 1: Skill Eval For Variable Mapping

The course already includes this type of case in
`evals/skills/mmm-variable-mapping/cases.yaml`.

The severe failure mode is substring matching:

```text
"Spends" in column name = media spend
```

That shortcut catches owned media, competitor media, aggregate channels, and
sometimes non-media fields. A model might still run, but the interpretation
would be wrong.

This is a good eval because it tests a real analytical hazard:

- role classification
- duplicate channel representation
- mediator/control confusion
- leakage from outcome decomposition
- artifact production before modeling

It is not testing whether the agent writes nice prose. It is testing whether
the agent preserves modeling judgment.

---

## Example 2: Skill Eval For Calibration Strategy

Week 4 introduces another dangerous failure: over-applying experiment evidence.

Suppose a geo experiment estimates that paid social increased revenue by 6% in
three treatment geos over four weeks, with a 95% interval of -2% to 14%.

A weak agent response might say:

```text
Paid social works. Use this as the paid social ROI prior and increase budget.
```

A good behavior eval should forbid that leap.

```yaml
cases:
  - id: calibration-wide-interval-not-strong-prior
    skill: calibration-strategy
    prompt: |
      A geo experiment for paid social ran for four weeks in three treatment
      geos and three controls. Incremental revenue lift was 6%, with a 95% CI
      of [-2%, 14%]. The MMM covers national weekly data for the last two
      years. Create a calibration handoff.
    expected:
      - Records the point estimate and uncertainty interval.
      - Notes that the interval includes zero.
      - Compares experiment geos and timing with the national MMM scope.
      - Recommends a weak calibration role such as sensitivity check,
        guardrail, diagnostic comparison, or context only.
      - Preserves transferability limits and implementation caveats.
    disallowed:
      - Treats the result as a precise channel ROI prior.
      - Recommends increasing paid social budget from this evidence alone.
      - Drops the confidence interval.
      - Claims the experiment proves national long-term paid social ROI.
```

Notice the shape: the eval does not need a perfect numerical answer. It needs
the agent to preserve the caveats that prevent bad business decisions.

---

## Choose The Scorer Before You Choose The Tool

Different claims need different scorers.

| Claim type | Better scorer | Avoid |
|---|---|---|
| File exists | Deterministic file check | LLM judge |
| YAML is valid | Parser/schema check | LLM judge |
| Required artifact sections exist | Markdown heading or schema check | Free-form opinion |
| Number is within tolerance | Numeric assertion | LLM judge |
| Forbidden phrase or recommendation absent | Regex/string check plus review | Only asking the model |
| Caveat is specific enough | Human review or calibrated LLM judge | Regex alone |
| Recommendation matches evidence strength | Human review or rubric judge | Pure keyword checks |

Rule of thumb:

> Use deterministic checks for facts. Use human or LLM judges for judgment.

An LLM judge is useful when the output requires interpretation, such as whether
a caveat is specific enough or whether a recommendation is overstated. It is
not useful for checking whether a file exists, whether YAML parses, or whether a
number equals the expected value.

---

## A Tiny Deterministic Scorer

You do not need a full eval platform to start. A small Python scorer can catch
many course failures.

```python
from pathlib import Path
import yaml


def load_cases(path):
    with open(path, "r", encoding="utf-8") as f:
        return yaml.safe_load(f)["cases"]


def score_text(output, expected_terms, disallowed_terms):
    missing = [term for term in expected_terms if term.lower() not in output.lower()]
    forbidden = [term for term in disallowed_terms if term.lower() in output.lower()]
    return {
        "passed": not missing and not forbidden,
        "missing": missing,
        "forbidden": forbidden,
    }


cases = load_cases("evals/skills/mmm-variable-mapping/cases.yaml")
output = Path("runs/example-output.md").read_text(encoding="utf-8")

result = score_text(
    output=output,
    expected_terms=["competitor", "duplicate", "Variable Map"],
    disallowed_terms=["use every spend column", "budget increase"],
)

print(result)
```

This is intentionally simple. It will miss nuance and can be gamed by surface
phrases. But it is transparent, cheap, and good enough for many mechanical
checks.

Use a human review or rubric judge for the final interpretation.

---

## Run Records: Make The Eval Reproducible

Every eval run should leave a small record. Without a run record, you cannot
tell whether a failure came from the skill, the prompt, the model, the
workspace, or the scorer.

Use this shape:

```text
case id:
date:
agent / harness:
model:
prompt version:
skill version or commit:
workspace commit / snapshot:
input files:
artifacts produced:
scorer:
pass/fail:
unexpected behavior:
human review notes:
next action:
```

For course work, a run record can be a Markdown note. For production work, it
often becomes a row in a dataset or an entry in an eval tracking tool.

---

## Statistics For Evals

Evals are measurements, so they inherit the same statistical issues you have
seen in experiments and MMM: sampling error, dependence, noisy outcomes,
selection bias, and overconfident interpretation.

Most capstone evals will be small. That is fine. Small evals are useful when
they are described honestly:

```text
This eval catches a specific severe failure mode.
It does not estimate general agent reliability.
```

This section draws on Cameron Wolfe's
[Applying Statistics to LLM Evaluations](https://cameronrwolfe.substack.com/p/stats-llm-evals)
and the Anthropic paper it summarizes,
[Adding Error Bars to Evals](https://ar5iv.labs.arxiv.org/html/2411.00640).
The important shift is to treat evals like experiments, not scoreboard rows.

### Think In Terms Of A Super-Population

An eval set is a finite sample from a larger universe of possible tasks. Your
three calibration cases are not "all possible calibration failures." They are a
sample of the failure modes you thought to test.

That means an eval score estimates performance on an underlying skill:

```text
finite eval cases -> estimate of broader workflow behavior
```

For this course, the broader skill might be:

- classifying MMM variables without leakage
- preparing Meridian inputs without shape or missing-data errors
- translating experiment evidence without overclaiming calibration strength
- producing stakeholder recommendations that preserve uncertainty

The eval set is only useful if it represents that skill. A large eval set of
near-duplicate cases can still give a misleading sense of coverage.

### Define The Unit Of Analysis

Before calculating anything, decide what one observation means.

Common choices:

| Unit | Example | Use when |
|---|---|---|
| Run | One agent attempt on one case | You care about stochastic flakiness. |
| Case | One scenario, possibly with repeated runs | You care about coverage of distinct failure modes. |
| Skill | A bundle of cases for one skill | You care whether a workflow asset is ready. |
| Artifact | One produced Variable Map or Calibration Handoff | You care whether durable output meets a contract. |

Do not mix these up. Five reruns of one case are evidence about that case's
stability. They are not the same as five different eval cases.

### Pass Rates Need Uncertainty

For binary evals, report:

```text
passes / total
pass rate
uncertainty or caveat
unit of analysis
```

If you run 3 cases and pass 3, do not say "the skill is 100% reliable." Say:

```text
The skill passed 3/3 targeted cases. These cases cover competitor-spend
misclassification, duplicate channels, and outcome leakage. This is not a
general reliability estimate because the eval set is small and targeted.
```

If cases are independent and binary, the pass rate is a sample mean. A first
standard error approximation is:

```text
p_hat = passes / n
SE = sqrt(p_hat * (1 - p_hat) / n)
95% CI approx = p_hat +/- 1.96 * SE
```

That formula is useful for intuition, but it behaves badly when `n` is small or
the pass rate is close to 0 or 1. With larger eval sets, use Wilson confidence
intervals rather than naive normal intervals for binary pass rates.

The Wilson interval is useful because it does not pretend that `3/3` means
perfect certainty. A rough interpretation is:

```text
3/3 passing cases is compatible with a wide range of true pass rates.
30/30 passing cases is stronger evidence, but still not proof of perfection.
```

A compact Wilson helper:

```python
from math import sqrt


def wilson_interval(passes, total, z=1.96):
    if total == 0:
        raise ValueError("total must be positive")

    p_hat = passes / total
    denom = 1 + z**2 / total
    center = (p_hat + z**2 / (2 * total)) / denom
    half_width = (
        z
        * sqrt((p_hat * (1 - p_hat) / total) + (z**2 / (4 * total**2)))
        / denom
    )
    return center - half_width, center + half_width


print(wilson_interval(3, 3))
print(wilson_interval(8, 10))
```

You do not need to compute intervals for every classroom exercise. You do need
the habit: small pass counts are weak evidence.

For non-binary scores, such as 1-5 rubric scores, use the usual sample mean and
standard error:

```python
from math import sqrt
import statistics as stats


def mean_se_ci(scores, z=1.96):
    mean = stats.mean(scores)
    sd = stats.stdev(scores)
    se = sd / sqrt(len(scores))
    return {
        "mean": mean,
        "se": se,
        "ci_95": (mean - z * se, mean + z * se),
        "n": len(scores),
    }
```

Only use this simple CLT-style interval when the cases are roughly independent,
the score has finite variance, and `n` is not tiny. If cases are clustered or
repeated versions of the same task, the interval will usually be too narrow.

### Repeated Runs Measure Flakiness

Agent outputs can vary. A workflow may pass once and fail the next time because
the model took a different route, skipped a file, or phrased a caveat too
weakly.

Useful defaults:

- Run a normal case once while developing it.
- Run a severe failure case 3 times before claiming the behavior is stable.
- Run a formal comparison 5 or more times per case if reliability is the point.

Report repeated runs inside the case:

```text
case: calibration-wide-interval-not-strong-prior
runs: 3
passed: 2
failed: 1
failure mode: one run recommended a paid social budget increase despite wide CI
```

Then summarize across distinct cases. Do not flatten repeated runs into the same
pool as separate failure modes.

### Two Sources Of Variance

Wolfe's summary of the Anthropic eval-statistics paper is especially useful on
variance. An eval score varies for two reasons:

1. Some cases are intrinsically easier or harder than others.
2. The model or judge may produce different outputs on repeated runs of the
   same case.

In course language:

```text
between-case variance: competitor-spend trap vs calibration-transfer trap
within-case variance: same prompt passes once, then omits uncertainty next run
```

Adding more distinct cases reduces uncertainty about the broader skill.
Repeating the same case reduces uncertainty about stochastic flakiness on that
case. Both are useful, but they answer different questions.

If one case is important enough to rerun, score at the case level first:

```text
case A: 3/3 runs pass -> case pass
case B: 2/3 runs pass -> case unstable or partial fail
case C: 0/3 runs pass -> case fail
```

Then summarize the set of cases. This keeps repeated runs from pretending to be
new coverage.

Do not reduce temperature just to make evals look stable. If the production
workflow uses a certain model configuration, the eval should usually test that
configuration. Lowering randomness changes the behavior being measured.

### Paired Comparisons Beat Unpaired Comparisons

If you compare two prompts, skills, models, or harnesses, use paired cases:

- same prompt
- same files
- same skill or skill version
- same workspace snapshot
- same scorer

The reason is simple: some cases are harder than others. If model A gets easy
cases and model B gets hard cases, the comparison is not meaningful.

For binary pass/fail comparisons, the most informative rows are the
disagreements:

| Case result | Meaning |
|---|---|
| A passes, B passes | Both handled the case. |
| A fails, B fails | The case is hard or the scorer is strict. |
| A passes, B fails | Evidence in favor of A on this case. |
| A fails, B passes | Evidence in favor of B on this case. |

With enough paired binary cases, McNemar's test is a standard option because it
focuses on the discordant pairs. For small course evals, a table of discordant
cases plus human review is usually more honest than a formal significance
claim.

Avoid comparing two models by asking whether their separate confidence intervals
overlap. That test is conservative and can miss real paired differences. If the
same cases were used for both workflows, analyze the case-level differences
directly.

### Score Differences, Not Just Scores

Some evals produce numeric scores, such as a 1-5 rubric score for caveat
quality. When comparing two workflows on the same cases, compute the paired
difference per case:

```text
case 1: A - B = +1
case 2: A - B = 0
case 3: A - B = -1
case 4: A - B = +2
```

Then summarize the distribution of differences. With a larger set, use a paired
bootstrap or paired permutation test. With a small class exercise, inspect the
cases where the difference is large and ask why.

A compact paired-difference helper:

```python
from math import sqrt
import statistics as stats


def paired_difference_summary(scores_a, scores_b, z=1.96):
    if len(scores_a) != len(scores_b):
        raise ValueError("paired scores must have the same length")

    diffs = [a - b for a, b in zip(scores_a, scores_b)]
    mean_diff = stats.mean(diffs)
    se = stats.stdev(diffs) / sqrt(len(diffs))
    return {
        "mean_diff": mean_diff,
        "se": se,
        "ci_95": (mean_diff - z * se, mean_diff + z * se),
        "n_pairs": len(diffs),
    }
```

If the 95% interval for the mean difference includes zero, the eval set does not
give strong evidence that one workflow outperformed the other. That does not
mean they are equal. It means this eval set did not resolve the difference.

### Clusters Make Evidence Less Independent

Many eval cases are variants of the same underlying task. Treat them as a
cluster, not as fully independent evidence.

For example, ten variable-mapping prompts that all test the same competitor
spend trap are useful for debugging that trap. They do not prove the skill works
for all MMM setup failures.

A better eval set covers different failure families:

- competitor spend misclassified as owned media
- aggregate and component channels included together
- media volume treated as an external control
- outcome decomposition used as a predictor
- post-treatment mediator treated as a confounder
- missing data ignored before Meridian setup
- noisy experiment converted into a strong calibration prior

When clusters exist, the safest classroom move is to summarize by cluster:

```text
variable-role failures: 4/5 cases passed
Meridian setup failures: 2/3 cases passed
calibration-overclaim failures: 1/3 cases passed
```

For a rough cluster-adjusted calculation, average within each cluster first,
then compute uncertainty across cluster means. This treats each failure family
as the independent unit:

```python
import statistics as stats
from math import sqrt


cluster_scores = {
    "variable_roles": [1, 1, 1, 1, 0],
    "meridian_setup": [1, 1, 0],
    "calibration": [1, 0, 0],
}

cluster_means = [stats.mean(scores) for scores in cluster_scores.values()]
mean = stats.mean(cluster_means)
se = stats.stdev(cluster_means) / sqrt(len(cluster_means))

print({"cluster_adjusted_mean": mean, "se": se, "clusters": len(cluster_means)})
```

This is not a substitute for a formal clustered standard error in serious
research, but it teaches the right caution: related cases should not be counted
as fully independent.

When you report results, name the clusters covered and the clusters missing.

### Judge Accuracy Is Its Own Eval

If an LLM judge scores outputs, evaluate the judge against human labels before
trusting it.

At minimum, build a small calibration set:

| Output | Human label | Reason |
|---|---|---|
| A | Pass | Preserves interval and transferability caveat. |
| B | Fail | Drops uncertainty. |
| C | Fail | Recommends budget increase without diagnostics. |
| D | Pass | Caveats are present, but concise. |

Then check:

- false passes: judge accepts a bad output
- false fails: judge rejects a good output
- failure recall: among known failures, how many did the judge catch?
- agreement rate: how often did the judge match the human labels?

For larger judge calibration sets, report Cohen's kappa for two raters or
Krippendorff's alpha for multiple raters or incomplete labels. For this course,
the key idea is simpler: a judge that misses obvious causal overclaims should
not be used to grade causal caveats.

### Sample Size And Claims

The smaller the eval set, the narrower the claim should be.

| Evidence | Reasonable claim | Overclaim |
|---|---|---|
| 1 passing run | This run passed. | The workflow is reliable. |
| 3/3 targeted cases | The skill handled these known traps. | The skill has 100% accuracy. |
| 20 varied cases with intervals | The skill appears stable on this eval set. | The agent is generally safe. |
| Paired cases across two models | Model A did better on these cases. | Model A is universally better. |

Good eval reporting sounds modest because the evidence is usually narrow.

### Beware Multiple Comparisons

If you try many prompts, models, scorers, or skill edits, eventually one version
will look best by chance. This is the same problem as repeatedly searching a
model specification until a coefficient looks good.

Use two sets when the stakes are high:

- development evals: cases you use while editing the prompt or skill
- holdout evals: cases you run after choosing the version

For capstone work, you do not need a large formal holdout. But you should still
avoid reporting only the best-looking run after many attempts. Report the
number of attempts, the final version, and any severe failures discovered along
the way.

### Minimum Statistical Reporting Template

Use this shape when you summarize an eval:

```text
Eval set:
Unit of analysis:
Cases / runs:
Pass rate:
Uncertainty:
Failure clusters covered:
Repeated-run behavior:
Scorer:
Human review:
What this supports:
What this does not support:
```

Example:

```text
Eval set: calibration-strategy severe caveats
Unit of analysis: case, with repeated runs summarized inside each case
Cases / runs: 3 cases, 3 runs per case
Pass rate: 2/3 cases fully passed; 7/9 runs passed
Uncertainty: small targeted set, no broad reliability claim
Failure clusters covered: wide intervals, geo-to-national transferability,
budget recommendation overclaim
Scorer: deterministic required-term checks plus human review
Human review: a reviewer checked recommendation strength manually
What this supports: the skill catches several calibration overclaim traps
What this does not support: general safety across all experiment designs
```

---

## LLM Judges: Useful, But Not Ground Truth

LLM judges can help when the output is judgment-shaped:

- Did the handoff preserve the experiment's uncertainty?
- Did the final recommendation match the evidence strength?
- Did the explanation name transferability limits?
- Did the agent avoid unsupported causal language?

But judges need calibration. Before trusting a judge, compare it against a
small set of human-labeled outputs.

Ask:

- Does the judge agree with human labels on obvious passes?
- Does it catch obvious failures?
- Does it penalize missing caveats?
- Does it over-reward fluent but unsupported answers?

For course purposes, the simplest calibration is a small answer key:

```text
output A: pass
output B: fail, overclaims causality
output C: fail, drops uncertainty
output D: pass with minor caveat
```

Run the judge on those examples first. If it cannot match human labels on easy
cases, do not use it for grading or claims.

---

## Current Tooling Landscape

Start with the eval question, not the tool. The right tool depends on what you
need to score, where the workflow runs, and whether you need traces, CI, human
review, or production monitoring.

### Tool Categories

| Category | Examples | Best for | Weak fit for |
|---|---|---|---|
| Plain code checks | Python, `pytest`, YAML parsers | File existence, schemas, numeric tolerances, path rules, deterministic artifact checks | Subjective caveat quality |
| Prompt and provider test matrices | [Promptfoo](https://www.promptfoo.dev/docs/configuration/expected-outputs/) | Prompt variants, model/provider comparisons, assertion suites, custom Python or JavaScript scorers | Long sandboxed agent tasks |
| Formal eval frameworks | [Inspect AI](https://inspect.aisi.org.uk/), [OpenAI Evals](https://github.com/openai/evals) | Reusable datasets, solvers, scorers, model comparisons, more formal eval runs | One-off classroom checks |
| Sandboxed agent benchmarks | [Inspect AI](https://inspect.aisi.org.uk/), [Harbor](https://www.harborframework.com/docs/run-jobs/run-evals) | Tool-using agents, coding agents, file-system tasks, containerized environments, trajectories | Simple text-only prompt checks |
| Observability plus eval platforms | [Phoenix](https://arize.com/docs/phoenix), [Langfuse](https://langfuse.com/docs/evaluation/overview), [Braintrust](https://www.braintrust.dev/docs/evaluate), [Opik](https://www.comet.com/docs/opik/evaluation/overview) | Traces, datasets from real runs, experiments, human annotation, online monitoring, regression tracking | Tiny local evals with no app or trace stream |
| Pytest-style LLM app evals | [DeepEval](https://deepeval.com/docs/introduction) | Unit-test-like LLM evals, metrics, agent/tool-use checks, RAG/chatbot workflows, CI | Benchmarking base model capability |
| RAG and retrieval metrics | [Ragas](https://docs.ragas.io/en/latest/concepts/metrics/available_metrics/) | Context precision/recall, faithfulness, answer relevance, retrieval-heavy workflows | MMM workflows without retrieval |
| Model benchmark harnesses | [lm-evaluation-harness](https://github.com/EleutherAI/lm-evaluation-harness) | Standard academic benchmarks, base-model comparisons, reproducible leaderboard-style tasks | Course-specific artifact contracts |

### What To Use In This Course

Use this default stack for Week 4 and capstone work:

1. Write cases in YAML.
2. Use Python or `pytest` for deterministic checks.
3. Use human review for causal caveats and recommendation strength.
4. Add an LLM judge only after you have a small human-labeled calibration set.
5. Move to a framework only when the eval set becomes repetitive or needs CI,
   provider comparison, traces, or sandboxing.

That means a strong capstone eval can be very simple:

```text
cases.yaml
run-output.md
score_artifact.py
manual-review.md
```

Do not apologize for using plain files. If the failure mode is clear and the
scorer is appropriate, this is often better than a complex platform.

### When Each Tool Becomes Worth It

Use **Promptfoo** when you want a matrix:

```text
same eval cases x multiple prompts x multiple providers
```

Promptfoo is especially useful for assertions such as contains, regex, JSON
schema, custom scripts, model-graded checks, and derived metrics. It also has
Python integration, so students can reuse course-style Python scorers instead
of switching everything to JavaScript.

Use **Inspect AI** when the task needs a formal eval shape:

```text
dataset -> solver or agent -> scorer -> logs
```

It is a better fit when you need tool use, agent behavior, sandboxes, log
viewing, custom scorers, or repeated runs over many models. For example, an
Inspect eval could test whether an agent reads a data dictionary, creates a
Variable Map, writes only to allowed paths, and stops before fitting.

Use **Harbor** when the unit is a sandboxed agent task with an instruction,
environment, and verifier script. That is closer to coding-agent benchmarks
such as Terminal-Bench or SWE-Bench-style tasks than to a single prompt eval.
It is powerful, but usually heavier than needed for a Week 4 reading exercise.

Use **Phoenix**, **Langfuse**, **Braintrust**, or **Opik** when you need an
eval workflow around real application traces:

- capture traces and tool calls
- turn failures into datasets
- compare prompt or model variants
- attach human labels
- run online or offline scoring
- monitor quality over time

These tools become useful when agentic workflows are used repeatedly by a team.
They are not necessary for a single course artifact unless the project already
has traces or production-like runs.

Use **DeepEval** when you want pytest-style LLM application tests with ready-made
metrics and CI-friendly execution. It is useful for teams that want a Pythonic
testing workflow for agents, RAG systems, chatbots, MCP systems, or custom LLM
apps.

Use **Ragas** when retrieval is central. Its metrics cover RAG concerns such as
context precision, context recall, faithfulness, response relevance, and some
agent/tool-use cases. Most MMM capstones will not need Ragas unless the agent is
retrieving from a document store or knowledge base.

Use **OpenAI Evals** or the
[OpenAI platform evals](https://developers.openai.com/api/docs/guides/evals)
when your workflow is already centered on OpenAI models and you want to create
custom evals or track model changes over time. The open-source `openai/evals`
repo is also useful as a reference for eval structure.

Use **lm-evaluation-harness** when the question is about base model benchmark
performance on standard tasks. It is not the right default for "does this MMM
skill preserve causal caveats?" because that is an application- and
artifact-specific behavior.

### Tool Selection Checklist

Before adopting a framework, answer:

1. What is the unit under test?
2. Is the scorer deterministic, human, or LLM-as-judge?
3. Do we need traces or only final outputs?
4. Do we need to compare prompts, models, harnesses, or versions?
5. Do we need CI or production monitoring?
6. Can the tool preserve the artifact contract we care about?
7. Will the tool make the eval easier to understand, or just more impressive?

For this course, the last question matters most. Evals should make the
marketing measurement judgment more inspectable.

---

## In-Class Practice

In class, you will practice turning analytical failure modes into eval cases.
By the end, you should be able to:

1. Identify the severe failure a skill should prevent.
2. Decide whether the eval is testing a prompt, skill, artifact, harness, or
   final narrative claim.
3. Write `expected` and `disallowed` behavior that a reviewer can actually
   score.
4. Choose an appropriate scorer for each claim.
5. Summarize eval results without making claims beyond the evidence.

Useful failure modes to practice with:

- competitor spend treated as owned media
- clicks or views treated as external controls
- aggregate and component channels included together
- Meridian setup proceeds despite missing data
- noisy experiment used as a strong prior
- capstone recommendation ignores uncertainty

The class output should be a draft eval case and a short statement of what the
case does and does not prove.

---

## Capstone Deliverable Standard

For the capstone, each team should submit:

1. A skill or skill patch.
2. A behavior eval case for a severe failure mode.
3. A short run record.
4. A one-paragraph interpretation.

The interpretation should say:

- what the eval tested
- whether it passed
- what evidence was checked manually
- what the eval does not prove
- what should be added if this workflow were used again

Example:

```text
Our calibration-strategy eval passed in one reviewed run. It tested whether the
agent preserves uncertainty and avoids turning a small geo experiment into a
strong national prior. The review checked the handoff manually because the key
claim is judgment-shaped. This eval does not prove the skill handles every
calibration case; it only covers the wide-interval geo-lift failure mode.
```

---

## Common Anti-Patterns

Avoid these when writing evals:

1. **Testing generic helpfulness.**
   A good course eval tests a specific analytical failure.

2. **Using an LLM judge for facts.**
   Do not ask a judge whether a file exists or a number matches.

3. **Writing unscorable expectations.**
   "Be thoughtful" is not observable. "Names the confidence interval and says
   it includes zero" is observable.

4. **Overclaiming from small samples.**
   Passing 3/3 targeted cases is useful. It is not proof of general reliability.

5. **Ignoring artifacts.**
   If the workflow should produce a Variable Map, Model Spec, or Calibration
   Handoff, the eval should check that the artifact exists and contains the
   required decisions.

6. **Letting evals become stale.**
   When a skill changes, the eval should be rerun. When a new severe failure is
   discovered, add a case.

---

## Practice Questions

**Question 1:** A skill for Meridian setup correctly builds `media_spend`, but
silently uses a different channel order in `media`. Write one expected behavior
and one disallowed behavior for an eval case.

**Question 2:** A calibration handoff includes a point estimate but no
confidence interval. Should this be checked by a deterministic scorer, a human
reviewer, or an LLM judge? Explain.

**Question 3:** You run one eval case five times and the agent passes three
times. What can you claim? What can you not claim?

**Question 4:** A judge model gives high scores to outputs that sound confident
but omit causal caveats. What calibration set would you create before using
that judge again?

**Question 5:** For your capstone, name one severe failure mode and the artifact
where that failure should become visible.

---

## The One-Sentence Rule

An eval is a reusable question you ask of an agentic workflow:

> Did the workflow preserve the specific judgment that would make the analysis
> trustworthy?
