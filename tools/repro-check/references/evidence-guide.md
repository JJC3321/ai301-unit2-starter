# Evidence guide: where proof lives in a reproduction package

## Environment

**Where it lives**

- Eval bundle: the environment paragraph or line at the top of the candidate repro report; cross-check the issue body's stated OS/version and the repo-facts "latest release" / bug-template asks.
- Live mode: the draft repro comment's environment block; the issue body's environment section; `gh` / the issue page for the reporter's stated versions.

**What good looks like**

Enough named facts that a stranger can place the attempt: tool or app version, OS/platform, and any driver, shell, or backend the issue depends on. If the attempt used a different version than the issue, the difference is stated in the same place. A missing environment block on an environment-sensitive issue is not good.

## Steps

**Where it lives**

- Eval bundle: the numbered steps or command block in the candidate repro report; the issue body's reproduction steps for the intended trigger.
- Live mode: the draft repro comment's steps; the issue body's steps; any minimal fixture linked from the issue.

**What good looks like**

Steps run from a concrete starting state (cwd, fresh project, config file contents) through the exact trigger the issue names. Commands are copyable. A stranger does not need a private monorepo, an unshared config, or a guessed "set up the project" step. Skipping the issue's trigger syntax (e.g. a different flag form) is not followable for *this* issue.

## Behavior shown

**Where it lives**

- Eval bundle: output excerpts, logs, screenshots, or measurements inside the candidate repro report; expected vs actual lines; compare those artifacts to the error, crash, or UI behavior the issue describes.
- Live mode: the same sections of the draft repro; the issue body's expected/actual; thread comments that clarify correct behavior.

**What good looks like**

The artifact depicts the issue's behavior (same error class, same UI failure, same crash), not an adjacent one. A control run that shows healthy behavior next to the failing run is strong. "Tool started and printed a version banner" is not the blank-pane / crash / race the issue reported. If the report claims a crash, the artifact must show a crash — garbled-but-alive output is not a crash.

## Honesty

**Where it lives**

- Eval bundle: the claim comment's intent language plus the repro report's result statement ("reproduced", "could not reproduce", root-cause claims) read against the artifacts and steps in that same report.
- Live mode: the draft claim and repro comments the same way; do not credit files outside what will be posted.

**What good looks like**

The words match the proof. An evidenced cannot-reproduce that names what differed is honest and ready. "Guaranteed reproducible", "I verified this race", or "everyone has this" with no artifact is not. Certainty about a wrong artifact (wrong expression, wrong version, wrong syntax) is still dishonest relative to the issue.

## Comms

**Where it lives**

- Eval bundle: the candidate claim comment; the repo-facts "bug reports" template asks and "contribution policy" / AI policy line; the issue title for specificity.
- Live mode: the draft claim (and later repro) comment; `CONTRIBUTING.md` / `AI_POLICY.md` / issue templates on the repo; the live issue thread for house rules (Path Review: classmate claims do not block).

**What good looks like**

The claim is about *this* issue, with a modest concrete next step, not assign-me boilerplate or a +1. If the repo requires AI disclosure, the posted words disclose tool and human verification; silence fails only when disclosure is required. Template headings alone do not pass — the content under them must carry the proof families above.
