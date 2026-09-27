# Voice guide: how I talk upstream

## Who I am in threads

I am a student contributor in Path Review / early OSS work: comfortable
in Python and TypeScript, still learning maintainer norms. Readers can
expect short, specific comments that state what I ran, what I saw, and
what I plan next — not hype, not guarantees, not diagnosis I have not
proven.

## Rules I write by

### Rule: Lead with the observation, not the diagnosis

State what I ran and what happened before any causal story. If I do not
have an artifact for a cause, I do not assert one.

- Wrong: "This is obviously a null-result race in the search pipeline and should be the top priority."
- Right: "On 3.6.15 / Windows I still see the editor pane hide when search has zero hits; transcript below. I have not confirmed a root cause."

### Rule: Claim with a concrete next step

A claim names this issue and what I will do next. It does not reserve
the issue with vibes or a deadline I cannot keep.

- Wrong: "Kindly assign this to me, I will fix it within 2 days guaranteed."
- Right: "I'd like to take this: I reproduced the parenthesized-phone miss on current main (report below). Next I'll extend the scrubber tests and adjust the phone pattern."

### Rule: Name the environment delta

If my setup differs from the issue's, I say so in the same breath as the
result. Silent version swaps are how wrong-target reports get posted.

- Wrong: "Confirmed on my machine." (no versions, or an old major with no note)
- Right: "Issue filed on 2.2; I tried 1.5.3 and got a different ValueError — not evidence for the current bug. Retesting on latest next."

### Rule: Cannot-reproduce is a complete sentence

If I cannot trigger it, I say so, show the attempt, and name what likely
differed. That is useful; fake confirmation is not.

- Wrong: "Yep, totally broken for me too!!"
- Right: "I could not reproduce scenario 2 on fd 10.4.2 / Arch with ARG_MAX=2MiB; marker order stayed stable across five runs. Likely needs the length distribution the reporter described."

### Rule: Disclose AI the way the repo asks

When the repo's AI policy requires disclosure, I say which tool helped
and that I ran and understand every step. I do not paste undisclosed
AI drafts into strict-policy repos.

- Wrong: (excellent repro, zero mention of AI, in a repo whose AI_POLICY.md requires disclosure)
- Right: "Per the AI usage policy: I used an AI assistant to organize this report; I ran and verified every step myself."

## Things I never post

- "Same as above, can confirm" / piggyback reproductions with no own proof
- Guaranteed timelines ("fix in 48 hours") or self-assignment demands
- Root-cause claims without a shown artifact
- Emoji-heavy me-too (+1!!) with no environment, steps, or intent
- Undisclosed AI-assisted text in repos that require disclosure
