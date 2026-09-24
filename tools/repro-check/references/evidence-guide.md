# Evidence guide: where proof lives in a reproduction package


<!--
THIS IS THE PART YOU WRITE (new this week: week 1 handed you this file
finished; the scaffolding fades). The skill uses this guide as its map:
for every kind of proof a rubric check names, this file says WHERE to
find it in a package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repo-facts block, the
  claim comment, the repro report and its parts). In live mode (where
  on GitHub or in the draft: the issue thread, the repo's docs, the
  student's draft comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the versions named match what the
  issue targets, or the difference is called out") over adjectives
  ("environment is thorough").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute; your operator swap showed you what that feels
like. Write the map you wish your executor had.
-->
This is the map the rubric's checks read with. For each family of
proof, it says where to look — in an eval bundle and in live mode —
and what a sufficient piece of that proof looks like when you find it.

One section per check in `rubric.md`, same name, same order:
`environment`, `steps`, `behavior_shown`, `honesty`, `comms`. This file
says where the evidence is; the rubric's pass condition decides what
that evidence is worth, and its weights and verdict rule are the
rubric's alone. Where a section below reads stricter than the check it
serves, the rubric wins.

Two rules that apply to every family:

- **Read the artifact, not the sentence above it.** When the prose and
  the pasted output disagree, the output wins. Most bad packages read
  well; that is why they get posted.
- **Absent is not the same as insufficient.** Grade `unclear` only when
  the place this guide names is genuinely empty, not when what is there
  is weak. Weak evidence that fails the pass condition is a `fail`.

## Environment

<!-- Where the environment record lives, and what a sufficient one
looks like against the issue's stated target. -->

**Where it lives.** In an eval bundle: the `Environment:` line near the
top of the `## Candidate repro report`, plus any version or install
detail mentioned inside the steps. Read it against the `## Issue`
section's stated version, OS, and install method, and against the
`## Repo facts` block's `latest release` and `bug reports` lines (the
template names the environment fields that repo expects). Live: the
issue body's environment fields, the bug-report template in
`.github/ISSUE_TEMPLATE/`, the releases page for what "latest" means
today, and the student's draft report.

**What good looks like.** The record says enough to tell whether this
run happened under conditions relevant to the issue. The usual form is
the version of the thing under test, how it was installed, and the
platform it ran on, concrete enough that a reader could stand up the
same box (`fd 10.4.2 (pacman), Arch Linux (x86_64), kernel 6.15`) — but
the deciding question is narrower: are the axes the issue's behavior
depends on on the record? The shell for a shell-sensitive bug, the
driver for a driver-specific one, the build profile where debug and
release fail differently. A field the issue does not turn on can be
missing and the record is still sufficient; when the issue says the
behavior applies everywhere, any platform will do and only the version
matters.

Differences from the issue's target — an older release, a different OS,
a source build against a packaged one — are what the record has to
surface. A difference the report names itself can still be sufficient
evidence: the reader can judge it. A material difference left silent is
the failing shape, because the artifact is describing some other
version's behavior and nothing on the record says so. Immaterial
differences need no flagging; grade the ones that could change whether
the reported behavior appears.

## Steps

<!-- Where the reproduction steps live, and what makes them followable
by a stranger, starting state to trigger. -->

**Where it lives.** In an eval bundle: the `Steps:`, `Preparation:`,
and `Execution:` parts of the `## Candidate repro report` — the
numbered lines, the setup commands, and the commands inside the fenced
blocks (the fenced commands are the real steps; the prose around them
is a summary). Read them against the reproduction scenario, inputs, and
commands in the `## Issue` section, and against any trigger a
maintainer proposed in `## Thread highlights`. Live: the issue body's
steps, the thread's follow-ups, and the draft report.

**What good looks like.** Two things, and the second is the one that
gets forgotten. First, a stranger with the recorded environment could
run these commands in this order and arrive at the scenario the issue
describes. Starting state is shown or trivially reconstructible — the
fixture file is pasted, the config is inline, the setup loop is given.
Second, the steps are concrete enough that a reader can say what was
actually tested: which command, which input, which shape of the
scenario.

**Reaching the failure is not required here.** An attempt that ends in
a documented cannot-reproduce passes this check, as long as what was
attempted was the issue's scenario and the reader can see exactly what
ran. Whether the outcome amounts to evidence is `behavior_shown`'s
question; this check only asks whether the attempt is followable and
legible.

What fails is an attempt at a *different* scenario, or one nobody can
pin down. Every input the issue treats as material appears in the same
form the issue used: the same flag, the same syntax, the same file
contents. That is not a tidiness rule — a step that paraphrases,
tidies, or "simplifies" a critical input has quietly swapped the
scenario, and everything downstream is a test of something else. When
the issue names an exact command, compare the candidate's against it
character by character. Steps that run inside something the reader
cannot obtain — a private repo, an unshared config, an internal
fixture — establish nothing about what was tested, however precisely
they are written.

## Behavior shown

<!-- Where the artifacts live (output excerpts, logs, screenshots),
and what it means for an artifact to show the issue's behavior rather
than an adjacent one. -->

**Where it lives.** In an eval bundle: the fenced output blocks, logs,
screenshots, exit codes, and transcripts in the
`## Candidate repro report`, together with the `Expected:` and
`Actual:` lines that interpret them. Read them against the `## Issue`
section's description of actual and expected behavior and against any
output the reporter pasted. Live: the same artifacts in the draft, read
against the issue body's own output blocks.

**What good looks like.** The artifact carries the issue's specific
symptom, not a symptom: the same error text, the same exit code, the
same wrong or missing value, on the same code path. An artifact that
fails differently than the issue does — a graceful validation error
where the issue reports a panic, a compile error where the issue
reports a runtime one, garbled output where the issue reports a crash —
is evidence about a different bug, no matter how confidently the
`Actual:` line narrates it. An artifact that only shows the tool
running (a version banner, a session list, a successful startup) shows
nothing about the behavior at all. And where the artifact stops short
of the issue's symptom and the `Actual:` line carries the rest of the
way — the output shows a warning, the line calls it the reported data
loss — what the package has is an inference, not a demonstration; grade
the artifact.

**A documented cannot-reproduce is the other passing shape.** Where
the attempt ran the issue's scenario and the reported behavior simply
did not appear, absence is the result, and this check passes on
evidence of it: the artifacts show concretely what happened instead —
the command completing, the correct output, the exit code 0, the
prompt rendering right — and the report states in as many words how
that differs from what the issue reports. Both halves are required.
Artifacts with no statement of the difference leave the reader to
infer the outcome; a stated "could not reproduce" with nothing pasted
behind it is an assertion, not a result.

Two things that are not this. An **adjacent failure is not an
absence**: if the run hit some other error, the issue's behavior was
never tested for, and the grade is the same fail any adjacent artifact
earns — nothing here says the reported behavior would not have
occurred. And an artifact that shows only that the tool ran does not
demonstrate absence either; it demonstrates nothing in either
direction.

A **control run** is the strongest form this evidence takes: the
near-identical case that behaves correctly, shown beside the failing
one. It converts "this failed on my machine" into "this input differs
from that one in exactly the way the issue names." Its absence is not a
failure; its presence usually settles the check.

## Honesty

<!-- Where claims and their backing meet: how to tell a report that
says exactly what happened (including an honest cannot-reproduce) from
one that claims more than its evidence shows. -->

**Where it lives.** At the seam between the two candidate sections:
every assertion in the `## Candidate claim comment` read against what
the `## Candidate repro report` actually shows. Also the report's own
framing lines — `Result:`, `Expected:`/`Actual:`, and any "what
differed" paragraph — and any statement of cause, scope, prevalence, or
certainty anywhere in the package. Live: the draft claim comment
against the draft report, plus anything the student says in the thread.

**What good looks like.** Each sentence of the claim can be pointed at
a line of the report. Causes are marked as hypotheses unless an
artifact demonstrates them. Scope is no wider than what was tested: a
run on one build does not license a claim about another, and one
machine is not "everywhere". A **cannot-reproduce is a passing outcome**
when it is evidenced — it shows the real attempt, gives the artifacts
that attempt produced, names what differed from the reporter's
conditions, and stops short of concluding the bug is not real. Untested
parts of a multi-part issue are called out as untested rather than left
to look covered.

The mirror of that rule is the failure this check exists for: where
the artifacts establish only that an attempt did not reproduce the
issue, the package must not say it did. "Confirmed", "I can reproduce
this", "same crash here" over a report whose own output shows the
command succeeding, or failing in some other way, is a false
reproduction claim — and it fails here however careful the rest of the
report is. Read the `Result:` line last, after the artifacts have
already told you the outcome.

The test to apply: cover the report, read the claim comment alone, and
write down what a maintainer would now believe. Uncover the report. The
gap between the two is what this check grades.

## Comms


<!-- Where the words meet the repo: the claim comment against the
issue, the comments against the repo's stated templates and
contribution policy (including AI-use disclosure requirements), and
what specific-and-honest looks like next to boilerplate. -->

**Where it lives.** The `## Candidate claim comment`, read against
three things: the `## Issue` section (does it describe this issue
accurately?), the `## Repo facts` block's `bug reports` line (what the
template asks reporters for), and the `## Repo facts` block's
`contribution policy` line (what the repo requires of contributors,
including any AI-use rule). Live: `CONTRIBUTING.md`, any `AI_POLICY.md`
or "Use of AI" section, the issue templates, the existing thread, the
house rules in `scope.md`, and the student's own `voice-guide.md`.

**Which stage the package is at** is part of this check's evidence, and
it is read off the package, never assumed. The claim stage means the
claim comment stands alone: in live mode, `SKILL.md`'s claim-only draft
state, where no repro report exists yet. A package that carries a
`## Candidate repro report` alongside the comment is past that stage —
every eval bundle is, and so is a live full package.

**What good looks like.** The comment could only have been written by
the person who did this work: it says what they ran, what they found,
and what they intend to do next, in terms specific to this issue. It
does not promise a timeline, does not assert certainty the report does
not carry, and does not claim the issue in a way the repo's process
does not allow. Boilerplate that would fit any issue in any repo — an
assign-me with no stated next step, an enthusiastic confirmation with
nothing behind it — is not specific about this issue, and fails here
even when the attached report is good.

**At the claim stage, the comment promises rather than concludes.**
With no report behind it yet, a good claim comment says what the
student is going to do — attempt the reproduction, on what setup, and
report back — and stops there. A comment that instead announces the
outcome ("I can confirm this", "reproduced, assigning myself") is
asserting a result nothing in the package shows, and fails here.
Once the repro report ships with the comment, that same sentence is
the right one: "Reproduced on 15.2.0 (report below)" is a pointer to
attached evidence, not an unbacked assertion, and it is graded against
the report by `behavior_shown` and `honesty`. Do not fail a
full-package claim comment for stating a result its own report
demonstrates.

**What the repo says outranks what good comments generally look like.**
Where a stated template, contribution policy, or disclosure rule speaks
to something, that rule is the standard; a generic expectation about
tone, length, or friendliness never overrides it and never fails a
comment on its own. Read the repo's requirement first, then ask whether
this comment meets it.

**On disclosure, read the policy's scope, not just its existence.**
Treat a course package as AI-assisted work, then ask what this repo's
stated rule covers:

- A rule requiring **all AI usage in any form** to be disclosed reaches
  issue comments. A package whose comments do not disclose fails,
  however strong its proof is.
- A rule scoped to **pull requests**, or one that explicitly states no
  disclosure ask for issue comments, does not reach a claim comment.
  Absence of disclosure there is not a failure.
- A rule that only assigns **responsibility** for AI-assisted work
  ("review and understand what you submit", "you are responsible for
  your contributions") imposes no disclosure duty, and silence
  satisfies it.
- A rule requiring comments to maintainers to be **in the
  contributor's own words** is met by a comment that reads as a
  person's own account of their own work, and broken by generated-
  sounding boilerplate — this one is about voice, not about a
  disclosure sentence.
- No stated AI policy means no disclosure requirement to check. Do not
  invent one.

**Live mode only.** `scope.md`'s house rules change one reading here: in
the course's Path Review repo, a classmate's existing claim does not
make an issue claimed, so posting an own claim on an already-claimed
issue is not a comms failure. Piggybacking on a classmate's
reproduction instead of posting one's own still is.
