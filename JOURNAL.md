## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/151

**Issue title:** Bias detector patterns are too narrow to match common phrasings #151

**Tier:** [X] Tier 1  [ ] Tier 2  [ ] Tier 3

I picked this issue because: 

1. The solution looks appropriate and feasible for my skillset:
- It is a 'Tier 1' issue, which is the guidance for someone like me who is making their first open source contribution
- I've double-check from a global project search that class and method defined in `bias_detector.py` is limited to just that file and it's unit tests `test_bias_detector.py` -- so limited complexity and blast radius for any fix
- The identified code is in Python, which I'm comfortable with 

2. I've confirmed that it is a live issue by rerunning `test_bias_detector.py`. As reported, 9 unit tests are failing. 

3. It does not have a PR that fixes it, so I'm not duplicating or wasting effort.


**Problem summary:**

The method checks reviews for two types of bias -- dismissive assessments (e.g., "bootcamp graduates lack rigor") or demographic stereotypes (e.g, "young developers can't handle complex systems") -- and returns `is_biased == True` if bias is detected.    

However the regex patterns for `DISMISSIVE_PATTERNS` and `DEMOGRAPHIC_PATTERNS` in `bias_detector.py` are too strict and miss other common phrasings that mean the same thing (e.g, "the candidate only attended a bootcamp, so this project lacks sufficient rigor"), resulting in some biased statements being marked as `is_biased == False`. 

My hypothesized solution is to make the regex less restrictive to catch a broader range of similar phrasing, so that those are properly assessed as `is_biased == True`. The known unknown is that I'm not familiar with regex so I don't know how effective the fix would be or how much effort is required. 

An alternative solution is to use LLM as a classifier, although the effort to implement and test this would likely elevate it to a higher issue tier. 

**Branch name:** fix/151-bias-detector-patterns

**Setup confirmation:** [X] App runs locally at localhost:5173

**Cohort ledger:** [X] Issue added to cohort ledger

## Week 8 — Reproduction & solution planning

**Reproduction commit link:** 

https://github.com/chungmengcheong/pathreview/commit/2ac590662e70251df38bef162ed06975af6ed95b


**Reproduction summary:**

I added an additional unit test `test_dismissive_bootcamp_language_rephrased_detected` to verify that the bug reporter's rephrasing ("The candidate only attended a bootcamp, so this project lacks the rigor of a formal CS education.") is incorrectly passing the bias test. 

The new unit test and another 9 tests cited by bug reporter are failing as reported, i.e., incorrectly classifying biased test as `False`. See output of running of `.venv/bin/pytest tests/unit/test_bias_detector.py -v` below:

```

====================================================== short test summary info ======================================================
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_dismissive_bootcamp_language_detected - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_dismissive_bootcamp_language_rephrased_detected - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_bootcamp_lacks_rigor_detected - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_demographic_assumption_age_detected - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_coding_bootcamp_variant - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_developer_vs_programmer_distinction - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_multiple_bias_indicators - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_negative_educational_claim - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_rich_poor_assumption - assert False is True
FAILED tests/unit/test_bias_detector.py::TestBiasDetector::test_assumption_vs_observation - assert False is True
=================================================== 10 failed, 23 passed in 0.17s ===================================================
```

**PLAN.md link:** [link to PLAN.md in your fork]

https://github.com/chungmengcheong/pathreview/blob/fix/151-bias-detector-patterns/PLAN.md


**Walkthrough video (recommended):** [link to your Loom video, ≤2 min — shared for early feedback]

No video

**Blockers or open questions:**

No blockers or open questions


## Week 9 — Solution building & PR submission

### Check-in 1 (mid-week)

**Current progress:**

Per the plan, I have:

1. Refactored the regex patterns to generalize the concept of `SOURCE`, `PERSON`, `NEG_QUALITY` and `DESIRED_PROPERTY` to capture their synoymns and plurals, 

```
    SOURCE = r"(?:(?:coding\s+)?bootcamp|self-taught|online\s+(course)?)"
    PERSON = r"(?:graduates?|developers?|programmers?|person|people)"
    NEG_QUALITY = r"(?:insufficient|inadequate|lack|lacking|lacks)"
    DESIRED_PROPERTY = r"(?:code|rigor|fundamentals|proper\s+training|preparation)"
```

and then standardize their usage across all the regex expressions so that the overall set of expressions are simplified and more easily maintainable, e.g., 

```
        rf"(?:{SOURCE})\s+(?:education|training)\s+(?:is\s+)?{NEG_QUALITY}",
        rf"(?:{SOURCE})\s+attendance\s+means\s+({NEG_QUALITY})\s+{DESIRED_PROPERTY}",
```

2. Added a new pattern to connect those concepts within a sentence boundary, so that they don't have to be immediately next to each other to trigger a bias flag, i.e.:
```
        rf"(?:{SOURCE})\b"
        r"(?:[^.]){0,40}?\b(?:so|because|since|thus|therefore|which\s+means)\b(?:[^.]){0,30}?\b"
        rf"{NEG_QUALITY}\s+"
        rf"(?:the\s+)?{DESIRED_PROPERTY}\b",
```

3. Added a new unit test `test_dismissive_bootcamp_language_rephrased_detected` to verify that the alternative rephrasing reported in #151 is correctly flagged as bias. 


**Next steps:**

The refactoring are passing the unit tests and have addressed the issue. 

However, regex patterns are still deterministic pattern matching and more limited compared to a natural language classifier. I'll create a future enhancement request to refactor this module to an AI classifier if there is sufficient need. 

**Blockers:**

---

### Check-in 2 (end of week)

**PR link:** [link to your submitted pull request]

**Branch:** [the branch name you worked on, e.g. `fix/123-short-description`]

**What you built:**
[1–3 sentences summarizing what your fix does and how it works]

**Tests added or updated:**
[Which test files did you touch? What do they cover?]

**Self-review confirmation:** [ ] make check passes  [ ] make test-unit passes

**Draft PR feedback received from:** [name or Slack handle, or "none"]