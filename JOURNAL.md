## Week 7 — Issue selection

**Issue link:** https://github.com/ascherj/pathreview/issues/151

**Issue title:** Bias detector patterns are too narrow to match common phrasings #151

**Tier:** [X] Tier 1  [ ] Tier 2  [ ] Tier 3

I picked this issue because: 

1. The solution looks appropriate and feasible for my skillset:
- It is a 'Tier 1' issue, which is the guidance for someone like me who is making their first open source contribution
- I've double-check from a global project search for that the class class and method defined in `bias_detector.py` is limited to just that file and it's unit tests `test_bias_detector.py` -- so limited complexity and blast radius for any fix
- The identified code is in Python, which I'm comfortable with 

2. I've confirmed that it is a live issue by rerunning `test_bias_detector.py`. As reported, 9 unit tests are failing. 

3. It does not have a PR that fixes it, so I'm not duplicating or wasting effort.


**Problem summary:**

The method checks reviews for two types of bias -- dismissive assessments (e.g., "bootcamp graduates lack rigor") or demographic steroetypes (e.g, "young developers can't handle complex systems") -- and returns `is_biased == True` if bias is detected.    

However the regex patterns for `DISMISSIVE_PATTERNS` and `DEMOGRAPHIC_PATTERNS` in `bias_detector.py` are too strict and miss other common phrasings that mean the same thing (e.g, "the candidate only attended a bootcamp, so this project lacks sufficient rigor"), resulting in some biased statements being marked as `is_biased == False`. 

My hypothesized solution is to make the regex less restrictive to catch a broader range of similar phrasing, so that those are properly assessed as `is_biased == True`. The known unknown is that I'm not familiar with regex so I don't know how effective the fix would be or how much effort is required. 

An alternative solution is to use LLM as a classifier, although the effort to implement and test this would likely elevate it to a higher issue tier. 

**Branch name:** fix/151-bias-detector-patterns

**Setup confirmation:** [X] App runs locally at localhost:5173

**Cohort ledger:** [X] Issue added to cohort ledger

