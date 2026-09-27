Reproduction report for #53.

Result: reproduced. `(555) 123-4567` is not redacted by `scrub()` and not found by `detect()`, while `555-123-4567` is handled by both. This matches the issue.

**Environment**
- Code: `codepath/pathreview-ai301-fa26-s3` at commit `2f4e82f` (current `main`)
- OS: Windows 11 (10.0.26200), 64-bit
- Python: 3.12.1 in a local `.venv` (`python -m venv .venv`)
- Deps: `pip install structlog pytest` (enough for this module and its unit tests; no Docker / Postgres required)
- pytest: 9.1.1

**Steps**

1. From the repo root, run the issue snippet plus a dashed-format control:

    $ .venv\Scripts\python -c "from safety.pii_scrubber import PIIScrubber; s = PIIScrubber(); print('scrub :', repr(s.scrub('Call me at (555) 123-4567 or 555-123-4567'))); print('detect paren:', s.detect('Call me at (555) 123-4567')); print('detect dashed:', s.detect('Call me at 555-123-4567'))"
    scrub : 'Call me at (555) 123-4567 or [REDACTED]'
    detect paren: []
    detect dashed: [{'type': 'phone_us', 'value': '555-123-4567', 'start': 11, 'end': 23}]

2. Phone-focused unit tests (xfail markers left on):

    $ .venv\Scripts\python -m pytest tests/unit/test_pii_scrubber.py -k phone -v --tb=no
    test_us_phone_number_redaction XFAIL
    test_us_phone_formats XFAIL
    test_international_phone_redaction PASSED
    test_detect_phone_pii XFAIL
    test_phone_at_start_of_text XFAIL
    test_phone_at_end_of_text PASSED
    2 passed, 19 deselected, 4 xfailed

3. Same four issue-named tests with `--runxfail` so the assertions show:

    $ .venv\Scripts\python -m pytest tests/unit/test_pii_scrubber.py --runxfail -q --tb=line -k "test_us_phone_number_redaction or test_us_phone_formats or test_detect_phone_pii or test_phone_at_start_of_text"
    E AssertionError: assert '[REDACTED]' in 'Call me at (555) 123-4567'
    E AssertionError: assert '[REDACTED]' in 'Contact: (555) 123-4567'
    E assert 0 > 0
    E AssertionError: assert '[REDACTED]' in '(555) 123-4567 is my phone number.'
    4 failed, 21 deselected

**Expected vs actual**
- Expected: both phone formats become `[REDACTED]` under `scrub()`, and `detect()` returns a `phone_us` entry for the parenthesized form.
- Actual: the parenthesized form passes through unchanged; `detect()` returns `[]` for it; the four named tests fail when the xfail marker is disabled. The dashed control still works.

I have not changed any application code. Next I will read the `phone_us` pattern in `safety/pii_scrubber.py` and propose a minimal fix that covers the parenthesized format without regressing the formats that already pass.
