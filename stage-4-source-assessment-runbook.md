# Source Assessment Runbook Maintenance

This document provides instructions for maintaining and extending the source assessment runbook based on discoveries made during individual project source assessments. The goal is to continuously improve the knowledge base used for interpreting C/C++ source code build characteristics by documenting new patterns and refining existing ones.

For every C/C++ source code project analyzed:
1. Read the existing `source-assessment-runbook.md` to understand existing patterns and approaches for interpreting C/C++ source code build characteristics.
2. For each `<file name>.source-assessment.md` created in Stage 1:
  - Create `<file name>.source-assessment-runbook.proposal.md`
  - Assess if any source code constructs encountered need a new runbook pattern or merit a revision to existing patterns
  - If the existing runbook is sufficient to cover all source code constructs analyzed, document that no new patterns are needed
  - Update any reference TODOs in the source assessment files with appropriate pattern references

## Source Assessment Runbook Maintenance (`<file name>.source-assessment-runbook.proposal.md`)

The source assessment runbook maintenance captures new knowledge about how to understand and interpret C/C++ source code constructs for build purposes. This builds a reusable knowledge base that improves the quality and consistency of future source code assessments by documenting patterns for recognizing what different source code constructs require from the build system. If the existing runbook sufficiently covers the source code constructs encountered during analysis, note that and do not feel obligated to propose new patterns.

### Source Assessment Pattern Guidelines
* Only propose new source assessment patterns when the existing runbook does not adequately cover source code constructs encountered during this project assessment
* Propose new source assessment patterns based on source code constructs encountered during this project assessment
* Propose improvements, clarifications, and extensions to existing source assessment patterns where evidence from the current analysis reveals new ways source code imposes build requirements
* Base all proposals on actual project experience, not theoretical scenarios and best practices
* Structure proposals into logically consistent sections that can be integrated into the main source assessment runbook
* Focus on a single, specific C/C++ source code concept that imposes one logical build requirement (e.g., header inclusion, library dependency declaration, or compiler attribute usage)
* Avoid combining multiple logically distinct source code concepts into a single pattern, even if they commonly appear together in the same source file
* Use generic, project-agnostic source code examples that exemplify the pattern rather than including project-specific names, paths, or configuration details
* Ensure proposed source assessment patterns can be validated and applied consistently across different projects

### Source Assessment Pattern Contents
* For each proposed source assessment pattern:
  - **Reference Tag**: uniquely identify the pattern (e.g., `[PROPOSED-SAP-<CATEGORY>-<NUMBER>]` for new, `[REVISION-SAP-<CATEGORY>-<NUMBER>]` for improvements)
  - **Pattern Description**: explain what this pattern means for the C/C++ source code's build requirements and compilation characteristics
  - **Pattern Identification**: describe the specific code constructs, syntax, or structural elements that indicate this pattern is present in the source code
  - **Source Example**: add project agnostic C/C++ source code snippet that demonstrates the pattern
  - **Build Requirement**: describe the specific build system requirement this pattern imposes (e.g., header dependency, library linking, compiler flag)
  - **Exceptional Cases**: list situations where the pattern identification criteria are met but additional source code context indicates a different build requirement should be applied. Format as a list where each exception includes:
    - **Exception Description**: explain what this exceptional case means for the build requirements when the additional context is present, and why it differs from the base pattern build requirement
    - **Exception Pattern Identification**: pattern identification criteria for recognizing when this exception applies
    - **Exception Source Example**: source example showing the base pattern plus the exceptional information
    - **Exception Build Requirement**: the alternative build requirement for this exceptional case
    - **Exception Handler Reference**: reference tag for the pattern that handles this exceptional case (or "NONE" if no pattern exists yet)

#### Example 1

````
### [SAP-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 2>
  - ...

- **Source Example**:
```c
<SOURCE CODE EXAMPLE>
```

- **Build Requirement**: <BUILD REQUIREMENT DESCRIPTION>

- **Exceptional Cases**: None.
````

#### Example 2

````
### [SAP-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Pattern Identification**: 
  - <ITEM 1>
  - <ITEM 2>
  - ...

- **Source Example**:
```c
<SOURCE CODE EXAMPLE>
```

- **Build Requirement**: <BUILD REQUIREMENT DESCRIPTION>

- **Exceptional Cases**: 
  - **Exception Description**: <DESCRIPTION>
  - **Exception Pattern Identification**:
    - <ITEM 1>
    - <ITEM 2>
    - ...
  - **Exception Source Example**:
```c
<SOURCE CODE EXAMPLE>
```
  - **Exception Build Requirement**: <EXCEPTION BUILD REQUIREMENT DESCRIPTION>
  - **Exception Handler Reference**: [SAP-<CATEGORY>-<NUMBER>]
````
