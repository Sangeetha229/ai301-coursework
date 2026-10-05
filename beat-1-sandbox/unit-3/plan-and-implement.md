# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`
---

## Posted upstream

**GitHub username**

`Sangeetha229`

**Plan comment**

**https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73#issuecomment-5985852927**

After reviewing the reproduction report, I searched the repository and found that `llm_provider` and the three `openrouter_*` settings are declared in `core/config.py:18-22`, but nothing else in the repository reads them. `ReviewGenerator` in `rag/generator/review_generator.py:25` is OpenRouter-capable, but nothing constructs it, so `OPENROUTER_API_KEY` is not currently used by the application.

I plan to make the documentation match the current implementation rather than add unsupported OpenRouter configuration:

* `README.md:24`: replace the instruction to add `OPENROUTER_API_KEY` with a note that the default mock setup requires no API key.
* `docs/SETUP.md:47`: make the same correction so it no longer says `OPENROUTER_API_KEY` is required for AI features.
* `.env.example`: leave unchanged. `OPENAI_API_KEY` stays because the OpenAI embedding provider reads it from the environment at `ingestion/embeddings/provider.py:77`.

I will not change `core/config.py`, provider code, or wire up OpenRouter. This plan is based on repository code search rather than testing with a real API key.

After the change, I will verify that the affected documentation no longer contains the unsupported OpenRouter setup instruction, `OPENAI_API_KEY` remains in `.env.example`, and the diff contains only `README.md` and `docs/SETUP.md`.

---

## Branch

**docs/73-llm-config-docs**

**Evidence**


### Before

Command:

```bash
grep -n OPENROUTER README.md docs/SETUP.md .env.example
```

Output:

```text
README.md:24:# Configure environment (add your OPENROUTER_API_KEY to .env)
docs/SETUP.md:47:# Edit .env and set your OPENROUTER_API_KEY (required for AI features)
```

This confirmed that both documentation files instructed users to configure `OPENROUTER_API_KEY`.

### After

Command:

```bash
grep -n OPENROUTER README.md docs/SETUP.md .env.example
```

Output:

```text
```

No `OPENROUTER` references remain in the checked files.

Command:

```bash
git diff -- README.md docs/SETUP.md
```

Output:

```diff
diff --git a/README.md b/README.md
index 7f16e1a..9f1d8a1 100644
--- a/README.md
+++ b/README.md
@@ -21,8 +21,9 @@ PathReview analyzes GitHub profiles, resumes, and project repositories to genera 
 git clone https://github.com/codepath/pathreview.git
 cd pathreview
 
-# Configure environment (add your OPENROUTER_API_KEY to .env)
+# Configure environment 
 cp .env.example .env
+# The default mock setup does not require an API key.
 
 # Start backing services — must be running before make setup
 docker compose up -d
diff --git a/docs/SETUP.md b/docs/SETUP.md
index 61674cb..88b990a 100644
--- a/docs/SETUP.md
+++ b/docs/SETUP.md
@@ -44,8 +44,8 @@ git remote add upstream https://github.com/codepath/pathreview.git
 
 # 2. Configure environment
 cp .env.example .env
-# Edit .env and set your OPENROUTER_API_KEY (required for AI features)
-# All other defaults work for local development
++# The default configuration works for local development
 
 # 3. Start backing services (PostgreSQL + Redis)
 #    ⚠  Docker must be running before the next step — make setup runs database migrations
```

## Eval iterations

**Run history**

I ran the evaluation incrementally, using partial runs to check the rubric against different evaluation packages before running the full evaluation.

* `pkg-01`: 1/1 agreement.
* `pkg-02` through `pkg-05`: 4/4 agreement.
* `pkg-06`, `pkg-07`, `pkg-09`, and `pkg-10`: 4/4 agreement.
* `pkg-11` through `pkg-15`: 4/5 agreement. `pkg-14` was the only disagreement in this run: the gold verdict was accept, but the skill returned reject. The failed checks were `diagnosis`, `file`, `approach` and `evidence_uncertainty`
* `pkg-16` through `pkg-20`: 5/5 agreement. `pkg-20` returned accept and matched the expected result for the final run.
* I then ran the full evaluation and saved the result to `eval-run.txt`.
* The final evaluation achieved 19/20 agreement, with `pkg-14` as the only disagreement.

The partial runs confirmed that the rubric consistently handled clear-accept, wrong-cause, scope-creep, unbuildable, and thread-convention cases. The remaining disagreement on `pkg-14` highlighted an edge case around how strictly the skill interprets implementation specificity and evidence uncertainty.

**Package analysis**

The evaluation packages showed that the skill needs to distinguish between technically valid plans and plans that have an unsupported diagnosis, excessive scope, missing implementation detail, weak testing, ignored risks, or unsupported assumptions. The 20 packages covered clear-accept, wrong-cause, scope-creep, unbuildable, and thread-convention cases.

The final evaluation achieved 19/20 agreement. The skill agreed with the gold verdict on all packages except `pkg-14`. The gold verdict was accept, but the skill returned reject and identified failures in `diagnosis`, `file`, `approach` and `evidence_uncertainty`.

The `pkg-14` result showed that the rubric can be stricter than the gold standard when a plan has strong reproduction evidence and a plausible implementation direction but intentionally leaves exact functions to be identified during code tracing. The other packages showed that the checks successfully identified incorrect diagnoses, scope creep, unbuildable plans, and plans that did not follow relevant conventions.

**Check rationale**

The final rubric uses eight required checks: `diagnosis`, `scope`, `file`, `approach`, `test_plan`, `risks`, `evidence_uncertainty`, and `comment_conventions`.

`diagnosis` verifies that the plan identifies the demonstrated underlying cause rather than only repeating the symptom. `scope` ensures the proposed work stays limited to the demonstrated problem. `file` and `approach` ensure that the plan provides useful implementation locations and a technically plausible path forward. `test_plan` requires observable tests that reproduce the original problem and verify the expected behavior after the change. `risks` checks for relevant regression, compatibility, performance, or behavioral risks. `evidence_uncertainty` ensures that confirmed facts are separated from hypotheses and unresolved questions. `comment_conventions` checks that the proposed issue comment follows applicable repository and contribution conventions.

All eight checks are required because a plan can be strong in one area while still being incomplete or unsafe in another. The final verdict is therefore binary: accept only when every required check passes; otherwise reject.

**Trade-offs**

A key trade-off was making the rubric strict enough to reject unsupported or incomplete plans while still allowing reasonable implementation details to remain open when investigation is still required. The `pkg-14` disagreement showed this tension: the plan provided strong evidence and a plausible approach but deferred exact functions until code tracing.

Another trade-off was requiring explicit uncertainty. This helps prevent hypotheses from being presented as confirmed facts, but it can make the rubric more conservative when a plan has a well-supported diagnosis without complete implementation-level evidence.

I also chose to keep all eight checks required rather than using weighted scoring. This makes the verdict easier to reproduce and prevents a strong score in one area from compensating for a critical weakness in another.

Finally, I kept the rubric focused on whether a plan is ready to build from rather than requiring a single specific implementation. This allows different technically valid approaches while still requiring evidence, concrete scope, testing, and clear implementation guidance.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
