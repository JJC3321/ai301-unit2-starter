# Unit 2 — Claim and reproduce

Live-mode claim + reproduction for Path Review issue
[#53](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53)
(`PII scrubber fails to redact parenthesized US phone numbers`), graded
with the installed `repro-check` skill whose eval harness passed at
**20/20** (`beat-1-sandbox/unit-2/eval-run.txt`).

GitHub username: `JJC3321`

## Run history

Ordered agreement scores from the harness runs that shaped this rubric:

1. `--include-calibration` practice run on the four class packages:
   **4/4** agreement with the activity labels (not scored).
2. Full scored run (`python run_eval.py --rubric … --evidence …
   --workers 5 --out results.json --save-run eval-run.txt`):
   **20/20** agreement (bar 18/20: **PASS**). Category floors:
   clear-accept 8/8, disclosure 1/1, no-evidence 4/4,
   unfollowable-comms 3/3, wrong-target 4/4. Transcript saved as
   `beat-1-sandbox/unit-2/eval-run.txt` (written 2026-09-26T22:17:34Z).
3. Local reproduction of Path Review #53 on commit `2f4e82f` (snippet +
   phone tests + `--runxfail` on the four named failures) — evidence
   captured in the Reproduction comment section below. No further
   rubric edits after step 2: every scored package already agreed with
   gold.

## Package analysis

Scored package: **`pkg-02`** (source: `sharkdp/bat#3845`, category
`wrong-target`).

| Side | Verdict |
|---|---|
| Gold label | `reject` |
| Skill (this rubric) | `reject` |

Gold note: the candidate ran a *prefix* range
(`--line-range '18446744073709551614:'`) instead of the issue's
*offset-from-end* trigger (`':-18446744073709551614'`), got a graceful
arg-validation error (exit 1), and narrated it as the reported capacity-
overflow panic (exit 101).

Skill reasoning (from `results.json`): `steps-rerunnable` failed because
the flag syntax is a different code path; `behavior-matches` failed
because the artifact is a validation error, not a capacity-overflow
panic; `honest-outcome` failed because the report claims "bat crashes
exactly as described" over the wrong artifact. `env-recorded`,
`claim-specific`, and `ai-disclosure` still passed — the package fails
on fidelity, not on polish. This is the wrong-target pattern the rubric
was written to catch, and it matches gold.

## Check rationale

Quoted check from `tools/repro-check/rubric.md` (same text as the
installed skill copy):

> **`behavior-matches`** — Evidence: The repro report's artifacts
> (command output, logs, screenshots, measurements) read against the
> behavior the issue describes; also any version or setup delta stated
> in the report vs the issue. Pass condition: The artifacts show the
> issue's described behavior (or a clearly labeled cannot-reproduce of
> that same trigger). Fail when artifacts show an adjacent symptom, a
> different error class, a graceful validation failure narrated as the
> reported crash, a silent environment/version deviation that changes
> what the artifact means, or "success" that only proves the tool runs.
> Weight: required.

Why this wording: the fail examples name the exact failure modes in
the gold set (`pkg-02` graceful validation as "crash", `pkg-08` compile
error instead of invalid-path behavior, `pkg-14` "tabs present"
narrated as the blank-pane race). Making "adjacent symptom / different
error class" explicit keeps the check from accepting confident prose
over the wrong proof. The optional cannot-reproduce clause lets an
honest negative still pass when the artifact is of the *same* trigger.

Applied to my #53 package: the snippet output
`'Call me at (555) 123-4567 or [REDACTED]'` plus empty `detect()` for
the parenthesized form is the issue's described behavior, not an
adjacent scrubber failure, so the check passes.

## Trade-offs

- Keeping all six checks `required` (no preferred-only softening) means
  a single missing environment block or a boilerplate claim rejects the
  whole package. That costs some near-miss "accept" packages in theory,
  but the scored set hit **20/20**, so nothing in the rubric was
  loosened after the final run.
- `behavior-matches` deliberately rejects graceful validation errors
  narrated as crashes. That gives up accepting packages that show *any*
  failure on a related flag; the gain is not rubber-stamping wrong-
  target reports like `pkg-02`.
- `ai-disclosure` is conditional on the repo's stated policy. On Path
  Review, `docs/CONTRIBUTING.md` has no disclosure rule, so silence
  passes — trading a universal "always disclose" rule for fidelity to
  each repo's written policy (which is what the disclosure gold item
  tests).
- Why nothing changed after the 20/20 run: every category floor was
  already full. Editing further would only risk introducing a new miss
  without fixing a disagreement.

## Claim comment

GitHub username: `JJC3321`

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53

Posted URL: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5852343602

Pasted claim comment text:

```text
Hi — I'd like to take the parenthesized US phone-number case in
`PIIScrubber` (`safety/pii_scrubber.py`) from this issue: `(555) 123-4567`
passes through `scrub()` unredacted and `detect()` returns `[]` for it,
while `555-123-4567` is redacted.

My next step is to reproduce on current `main` with the issue snippet
and the four named tests in `tests/unit/test_pii_scrubber.py`, post the
commands and output here, then look at the `phone_us` pattern.
```

## Reproduction comment

Posted URL: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53#issuecomment-5852357948

Pasted reproduction comment text (environment, steps, evidence):

~~~~text
Reproduction report for #53.

Result: reproduced. `(555) 123-4567` is not redacted by `scrub()` and
not found by `detect()`, while `555-123-4567` is handled by both. This
matches the issue.

Environment
- Code: `codepath/pathreview-ai301-fa26-s3` at commit `2f4e82f` (current `main`)
- OS: Windows 11 (10.0.26200), 64-bit
- Python: 3.12.1 in a local `.venv` (`python -m venv .venv`)
- Deps: `pip install structlog pytest` (enough for this module and its unit tests; no Docker / Postgres required)
- pytest: 9.1.1

Steps

1. From the repo root, run the issue snippet plus a dashed-format control:

```
$ .venv\Scripts\python -c "from safety.pii_scrubber import PIIScrubber; s = PIIScrubber(); print('scrub :', repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567'))); print('detect paren:', s.detect('Call me at (555) 123-4567')); print('detect dashed:', s.detect('Call me at 555-123-4567'))"
scrub : 'Call me at (555) 123-4567 or [REDACTED]'
detect paren: []
detect dashed: [{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]
```

2. Phone-focused unit tests (xfail markers left on):

```
$ .venv\Scripts\python -m pytest tests/unit/test_pii_scrubber.py -k phone -v --tb=no
test_us_phone_number_redaction XFAIL
test_us_phone_formats XFAIL
test_international_phone_redaction PASSED
test_detect_phone_pii XFAIL
test_phone_at_start_of_text XFAIL
test_phone_at_end_of_text PASSED
2 passed, 19 deselected, 4 xfailed
```

3. Same four issue-named tests with `--runxfail` so the assertions show:

```
$ .venv\Scripts\python -m pytest tests/unit/test_pii_scrubber.py --runxfail -q --tb=line -k "test_us_phone_number_redaction or test_us_phone_formats or test_detect_phone_pii or test_phone_at_start_of_text"
E AssertionError: assert '[REDACTED]' in 'Call me at (555) 123-4567'
E AssertionError: assert '[REDACTED]' in 'Contact: (555) 123-4567'
E assert 0 > 0
E AssertionError: assert '[REDACTED]' in '(555) 123-4567 is my phone number.'
4 failed, 21 deselected
```

Expected vs actual
- Expected: both phone formats become `[REDACTED]` under `scrub()`, and
  `detect()` returns a `phone_us` entry for the parenthesized form.
- Actual: the parenthesized form passes through unchanged; `detect()`
  returns `[]` for it; the four named tests fail when the xfail marker
  is disabled. The dashed control still works.

I have not changed any application code. Next I will read the `phone_us`
pattern in `safety/pii_scrubber.py` and propose a minimal fix that covers
the parenthesized format without regressing the formats that already
pass.
~~~~

## Issue link

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/53

## Self-check against the rubric (before posting)

Holding the pasted claim + repro comments above against each required
check (same rule the live skill uses):

| Check | Grade | Evidence |
|---|---|---|
| `env-recorded` | pass | Windows 11 10.0.26200, Python 3.12.1, commit `2f4e82f`, pytest 9.1.1 |
| `steps-rerunnable` | pass | Issue snippet and pytest commands are copyable from repo root |
| `behavior-matches` | pass | scrub leaves `(555) 123-4567`; detect returns `[]`; dashed control redacts |
| `honest-outcome` | pass | States "reproduced"; artifacts show that miss; no root-cause over-claim |
| `claim-specific` | pass | Names the parenthesized phone case and a concrete next step; no guarantee |
| `ai-disclosure` | pass | Path Review CONTRIBUTING has no disclosure rule |

Verdict under the rubric rule (all required pass): **accept** — ready to
post once `gh` is authenticated.
