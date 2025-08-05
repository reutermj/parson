# Bazel Mapping Runbook Maintenance

This document provides instructions for maintaining and extending the bazel mapping runbook based on discoveries made during individual project Bazel mappings. The goal is to continuously improve the knowledge base used for converting build system constructs to Bazel equivalents by documenting new patterns and refining existing ones.

For every build project analyzed:
1. Read the existing `bazel-mapping-runbook.<build system>.md` to understand existing patterns and approaches for converting build system constructs to Bazel.
2. Read the existing `build-assessment-runbook.<build system>.md` and any relevant runbook proposals `<file name>.build-assessment-runbook.proposals.md` to understand the build system constructs that need to be mapped.
3. For each `<file name>.build-assessment.md` completed in Stage 3:
   - Create `<file name>.bazel-mapping-runbook.proposals.md`
   - Assess if any Bazel mappings encountered need a new runbook pattern or merit a revision to existing patterns
   - If the existing runbook is sufficient to cover all Bazel mappings created, document that no new patterns are needed
   - Update any reference TODOs in the build assessment files with appropriate bazel mapping pattern references

## Bazel Mapping Runbook Maintenance (`<file name>.bazel-mapping-runbook.proposals.md`)

The bazel mapping runbook maintenance captures new knowledge about how to convert build system constructs to their Bazel equivalents. This builds a reusable knowledge base that improves the quality and consistency of future build system conversions by documenting patterns for recognizing how different build constructs should be mapped to Bazel. If the existing runbook sufficiently covers the Bazel mappings encountered during analysis, note that and do not feel obligated to propose new patterns.

### Bazel Mapping Pattern Guidelines
* Only propose new bazel mapping patterns when the existing runbook does not adequately cover Bazel mappings encountered during this project conversion
* Propose new bazel mapping patterns based on Bazel mappings encountered during this project conversion
* Propose improvements, clarifications, and extensions to existing bazel mapping patterns where evidence from the current analysis reveals new ways to accomplish build goals in Bazel
* Base all proposals on actual project experience, not theoretical scenarios and best practices
* Structure proposals into logically consistent sections that can be integrated into the main bazel mapping runbook
* Focus on a single, specific build system to Bazel mapping concept that accomplishes one logical purpose (e.g., mapping library targets, configuring compiler flags, or defining test executables)
* Avoid combining multiple logically distinct mapping concepts into a single pattern, even if they commonly appear together in the same build construct
* Use generic, project-agnostic source code examples that exemplify the pattern rather than including project-specific names, paths, or configuration details
* Use placeholder version numbers (e.g., "X.Y.Z") for bazel_dep dependency versions instead of specific version numbers
* Ensure proposed bazel mapping patterns can be validated and applied consistently across different projects

### Bazel Mapping Pattern Contents
* For each proposed bazel mapping pattern:
  - **Reference Tag**: uniquely identify the pattern (e.g., `[PROPOSED-BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]` for new, `[REVISION-BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]` for improvements)
  - **Pattern Description**: explain what this pattern means for converting the existing build system construct to its Bazel equivalent
  - **Build System Pattern Identification**: reference the specific build assessment patterns (e.g., `[BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`) from the assessment runbook that identify when this Bazel mapping pattern should be applied
  - **Source Example**: add project agnostic source code snippet from the original build system that demonstrates the pattern
  - **Bazel Mapping Description**: explain how the original build construct maps to Bazel, describing which Bazel rules, attributes, or mechanisms accomplish the same goals
  - **Bazel Code Example**: provide the equivalent Bazel BUILD.bazel file syntax that implements this mapping
  - **Output Bazel File**: specify the type of Bazel file where this construct should be placed (e.g., BUILD.bazel, MODULE.bazel, .bzl files)
  - **Exceptional Cases**: list situations where the pattern identification criteria are met but additional build script context indicates a different Bazel mapping should be applied. Format as a list where each exception includes:
    - **Exception Description**: explain what this exceptional case means for the Bazel mapping when the additional context is present, and why it differs from the base pattern mapping
    - **Exception Pattern Identification**: pattern identification criteria for recognizing when this exception applies
    - **Exception Source Example**: source example showing the base pattern plus the exceptional information
    - **Exception Bazel Mapping**: the alternative Bazel mapping for this exceptional case
    - **Exception Handler Reference**: reference tag for the pattern that handles this exceptional case (or "NONE" if no pattern exists yet)

#### Example 1

````
### [BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Build System Pattern Identification**: [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Bazel Mapping Description**: <BAZEL MAPPING DESCRIPTION>

- **Bazel Code Example**:
```<LANGUAGE>
<BAZEL CODE EXAMPLE>
```

- **Output Bazel File**: <BAZEL FILE TYPE>

- **Exceptional Cases**: None.
````

#### Example 2

````
### [BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Build System Pattern Identification**: [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]

- **Source Example**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Bazel Mapping Description**: <BAZEL MAPPING DESCRIPTION>

- **Bazel Code Example**:
```<LANGUAGE>
<BAZEL CODE EXAMPLE>
```

- **Output Bazel File**: <BAZEL FILE TYPE>

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
  - **Exception Bazel Mapping**: <EXCEPTION BAZEL MAPPING DESCRIPTION>
  - **Exception Handler Reference**: [BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]
````
