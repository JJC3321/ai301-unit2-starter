Hi — I'd like to take the parenthesized US phone-number case in
`PIIScrubber` (`safety/pii_scrubber.py`) from this issue: `(555) 123-4567`
passes through `scrub()` unredacted and `detect()` returns `[]` for it,
while `555-123-4567` is redacted.

My next step is to reproduce on current `main` with the issue snippet
and the four named tests in `tests/unit/test_pii_scrubber.py`, post the
commands and output here, then look at the `phone_us` pattern.
