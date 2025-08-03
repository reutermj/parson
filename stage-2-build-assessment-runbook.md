# Build Assessment Runbook Maintenance

This document provides instructions for maintaining and extending the build assessment runbook based on discoveries made during individual project assessments. The goal is to continuously improve the knowledge base used for interpreting build systems by documenting new patterns and refining existing ones.

For every build project analyzed:
1. Read the existing `build-assessment-runbook.<build system>.md` to understand existing patterns and approaches for interpreting the build system.
2. For each `<file name>.build-assessment.md` created in Stage 1:
  - Create `<file name>.build-assessment-runbook.proposal.md`
  - Assess if any build constructs encountered need a new runbook pattern or merit a revision to existing patterns
  - If the existing runbook is sufficient to cover all build constructs analyzed, document that no new patterns are needed
  - Update any reference TODOs in the build assessment files with appropriate pattern references

## Build Assessment Runbook Maintenance (`<file name>.build-assessment-runbook.proposal.md`)

The build assessment runbook maintenance captures new knowledge about how to understand and interpret build system constructs. This builds a reusable knowledge base that improves the quality and consistency of future build system assessments by documenting patterns for recognizing what different build constructs accomplish. If the existing runbook sufficiently covers the build constructs encountered during analysis, note that and do not feel obligated to propose new patterns.

### Build Assessment Pattern Guidelines
* Only propose new build assessment patterns when the existing runbook does not adequately cover build constructs encountered during this project assessment
* Propose new build assessment patterns based on build constructs encountered during this project assessment
* Propose improvements, clarifications, and extensions to existing build assessment patterns where evidence from the current analysis reveals new ways build systems accomplish their goals
* Base all proposals on actual project experience, not theoretical scenarios and best practices
* Structure proposals into logically consistent sections that can be integrated into the main build assessment runbook
* Focus on a single, specific build system concept that accomplishes one logical purpose (e.g., setting a library's public header, configuring version information, or defining shared object versioning)
* Avoid combining multiple logically distinct build system concepts into a single pattern, even if they commonly appear together in the same build construct
* Use generic, project-agnostic source code examples that exemplify the pattern rather than including project-specific names, paths, or configuration details
* Ensure proposed build assessment patterns can be validated and applied consistently across different projects

### Build Assessment Pattern Contents
* For each proposed build assessment pattern:
  - **Reference Tag**: uniquely identify the pattern (e.g., `[PROPOSED-BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]` for new, `[REVISION-BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]` for improvements)
  - **Pattern Description**: explain what this pattern means for the existing build system's behavior and intent
  - **Pattern Identification**: describe the specific code constructs, syntax, or structural elements that indicate this pattern is present in the build source code
  - **Source Example**: add project agnostic source code snippet that demonstrates the pattern
  - **Exceptional Cases**: list situations where the pattern identification criteria are met but additional build script context indicates a different build assessment should be applied. Format as a list where each exception includes:
    - **Exception Description**: explain what this exceptional case means for the build system's behavior and intent when the additional context is present, and why it differs from the base pattern build assessment
    - **Exception Pattern Identification**: pattern identification criteria for recognizing when this exception applies
    - **Exception Source Example**: source example showing the base pattern plus the exceptional information
    - **Exception Handler Reference**: reference tag for the pattern that handles this exceptional case (or "NONE" if no pattern exists yet)

#### Example 1

````
### [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 2>
  - ...

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Exceptional Cases**: None.
````

#### Example 2

````
### [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 2>
  - ...

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Exceptional Cases**: 
  - **Exception Description**: <DESCRIPTION>
  - **Exception Pattern Identification**:
    - <ITEM 1>
    - <ITEM 2>
    - ...
  - **Exception Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```
  - **Exception Handler Reference**: [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]
````
