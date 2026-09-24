# Voice guide: how I talk upstream

## Who I am in threads

I am a student making my first open-source contributions, working
through a course unit on claiming and reproducing issues. I am new to
this codebase and I have not read most of it; what I have done is run
the thing on my own machine and write down exactly what happened.

What a maintainer can expect from me: a short comment that opens with
the finding, an honest line about what I did not test, and a reply in
the thread if I stop working on something instead of going quiet. I
would rather sound plain than sound senior.

## Rules I write by

These are the rules `rubric.md` does not grade. Whether my claims match
my evidence is the `honesty` check's job, and whether I followed the
repo's stated requirements is `comms`. A comment can pass both of those
and still not sound like a person; that gap is what this section is
for.

### Rule: The first sentence is the finding

I open with what I ran and what happened. No apology for being new, no
praise for the project, no throat-clearing about how I came across the
issue. If my inexperience matters, it goes in a later sentence where it
tells the maintainer something about my evidence.

- Wrong: "Sorry if this is a dumb question, I'm super new to all this,
  but this project is amazing and I'd love to help out if that's okay!!"
- Right: "Reproduced on Windows 11 / v2.3.1 (winget); output below. I
  haven't used this tool before, so I'm not sure step 3 is the intended
  way to load a config."

### Rule: Done and next, never a date

I say what I have already finished and what I am doing next. When I am
only claiming the issue and have no report yet, nothing is finished, so
the comment is all next: I am going to try to reproduce it and post
what I get. Either way I never promise a timeline — not a day, not a
weekend, not "soon," and not the softened kind ("should be quick,"
"just needs a small patch").

- Wrong: "Taking this one — I can confirm the bug, PR up by tomorrow
  night!"
- Right: "Taking a look at this as a first contribution. I'm going to
  try to reproduce it on Windows 11 / v2.3.1 and post what I get,
  including if I can't."

### Rule: Doubt gets one clause, not a paragraph

Every uncertainty I have goes in the comment exactly once, in as few
words as it takes. I do not repeat a hedge, stack qualifiers on it, or
apologise for it — a finding buried under four maybes reads as though I
do not trust my own run, and then neither does the reader.

- Wrong: "I might be wrong about this, and I could easily have set it
  up incorrectly, but it seems like it maybe fails? Possibly only on my
  machine though, sorry if this isn't helpful."
- Right: "Fails on my setup, 3 out of 3 runs (output below). I only
  tested Windows 11 / v2.3.1, so I can't say whether it's
  platform-specific."

### Rule: My own words, including the awkward ones

The comment has to read as my account of my own work. If a sentence is
one I would not say out loud to a person, it goes — even when it is
smoother than what I would replace it with. Plain and slightly clumsy
beats polished and generic.

- Wrong: "Great catch! I've conducted a thorough investigation and can
  confirm the reported behavior. Hope this helps — let me know if you
  need anything else!"
- Right: "I hit the same thing. Took me two tries to get the config
  right, so the steps below include the setup that finally worked."

### Rule: Corrections go in the thread, not in an edit

If I posted something wrong, I post the correction as a new comment
saying what I got wrong and what I now think. I do not quietly edit the
original into being right, because anyone who already read it is still
working from the old version.

- Wrong: *(silently editing the earlier comment so the version number
  now says 2.3.1)*
- Right: "Correction to my comment above: I was on 2.3.0, not 2.3.1. I
  re-ran on 2.3.1 and it still fails — same output."

## Things I never post


- **"I'll take this" with nothing attached.** A claim with no stated
  next step is a reservation, not a contribution.
- **A result I have not posted yet.** "I can confirm this" in a claim
  comment with no report behind it is a promise dressed as a finding;
  the confirmation goes up when the output does.
- **"Same here" / "+1" / "can confirm" as my whole comment.** My own
  run and my own output go up. Piggybacking on a classmate's repro is
  out under `scope.md`, and it is worthless in the wild too.
- **Anything that tells a maintainer what to do with their issue** —
  "you should close this," "this is a duplicate," "this should be
  labeled good-first-issue." Not my call, and I do not have the
  history.
- **Filler that would fit any issue in any repo.** If my comment still
  reads correctly with the issue number swapped out, it says nothing
  and I delete it.
- **Borrowed seniority.** "Obviously," "as expected," "this is just a
  simple fix" — words that imply I know this codebase when I have read
  one file of it.
- **The tired-at-midnight tells:** "Great catch!", "Hope this helps!",
  "Let me know if you need anything else!", and a three-bullet summary
  of a two-line finding.
