# Bazel Source Mapping Runbook Maintenance

This document provides instructions for maintaining and extending the bazel source mapping runbook based on discoveries made during individual project Bazel source mappings. The goal is to continuously improve the knowledge base used for converting C/C++ source code constructs to Bazel equivalents by documenting new patterns and refining existing ones.

For every C/C++ source code project analyzed:
1. Read the existing `bazel-source-mapping-runbook.md` to understand existing patterns and approaches for converting C/C++ source code constructs to Bazel.
2. Read the existing `source-assessment-runbook.md` and any relevant runbook proposals `<file name>.source-assessment-runbook.proposals.md` to understand the source code constructs that need to be mapped.
3. For each `<file name>.source-assessment.md` completed in Stage 7:
   - Create `<file name>.bazel-source-mapping-runbook.proposals.md`
   - Assess if any Bazel source mappings encountered need a new runbook pattern or merit a revision to existing patterns
   - If the existing runbook is sufficient to cover all Bazel source mappings created, document that no new patterns are needed
   - Update any reference TODOs in the source assessment files with appropriate bazel source mapping pattern references

## Bazel Source Mapping Runbook Maintenance (`<file name>.bazel-source-mapping-runbook.proposals.md`)

The bazel source mapping runbook maintenance captures new knowledge about how to convert C/C++ source code constructs to their Bazel equivalents. This builds a reusable knowledge base that improves the quality and consistency of future source code conversions by documenting patterns for recognizing how different source code constructs should be mapped to Bazel. If the existing runbook sufficiently covers the Bazel source mappings encountered during analysis, note that and do not feel obligated to propose new patterns.

### Bazel Source Mapping Pattern Guidelines
* Only propose new bazel source mapping patterns when the existing runbook does not adequately cover Bazel source mappings encountered during this project conversion
* Propose new bazel source mapping patterns based on Bazel source mappings encountered during this project conversion
* Propose improvements, clarifications, and extensions to existing bazel source mapping patterns where evidence from the current analysis reveals new ways to satisfy source code build requirements in Bazel
* Base all proposals on actual project experience, not theoretical scenarios and best practices
* Structure proposals into logically consistent sections that can be integrated into the main bazel source mapping runbook
* Focus on a single, specific source code to Bazel mapping concept that satisfies one logical build requirement (e.g., mapping header dependencies, configuring library linking, or defining compilation flags)
* Avoid combining multiple logically distinct mapping concepts into a single pattern, even if they commonly appear together in the same source code construct
* Use generic, project-agnostic source code examples that exemplify the pattern rather than including project-specific names, paths, or configuration details
* Ensure proposed bazel source mapping patterns can be validated and applied consistently across different projects

### Bazel Source Mapping Pattern Contents
* For each proposed bazel source mapping pattern:
  - **Reference Tag**: uniquely identify the pattern (e.g., `[PROPOSED-BSMP-<CATEGORY>-<NUMBER>]` for new, `[REVISION-BSMP-<CATEGORY>-<NUMBER>]` for improvements)
  - **Pattern Description**: explain what this pattern means for converting the C/C++ source code construct to its Bazel equivalent
  - **Source Pattern Identification**: reference the specific source assessment patterns (e.g., `[SAP-<CATEGORY>-<NUMBER>]`) from the assessment runbook that identify when this Bazel source mapping pattern should be applied
  - **Source Example**: add project agnostic C/C++ source code snippet that demonstrates the pattern
  - **Build Requirement**: describe the specific build system requirement this source code pattern imposes
  - **Bazel Mapping Description**: explain how the source code construct's build requirements map to Bazel, describing which Bazel rules, attributes, or mechanisms satisfy the identified build requirements
  - **Bazel Code Example**: provide the equivalent Bazel BUILD.bazel file syntax that implements this mapping
  - **Output Bazel File**: specify the type of Bazel file where this construct should be placed (e.g., BUILD.bazel, MODULE.bazel, .bzl files)
  - **Exceptional Cases**: list situations where the pattern identification criteria are met but additional source code context indicates a different Bazel mapping should be applied. Format as a list where each exception includes:
    - **Exception Description**: explain what this exceptional case means for the Bazel mapping when the additional context is present, and why it differs from the base pattern mapping
    - **Exception Pattern Identification**: pattern identification criteria for recognizing when this exception applies
    - **Exception Source Example**: source example showing the base pattern plus the exceptional information
    - **Exception Build Requirement**: the alternative build requirement for this exceptional case
    - **Exception Bazel Mapping**: the alternative Bazel mapping for this exceptional case
    - **Exception Handler Reference**: reference tag for the pattern that handles this exceptional case (or "NONE" if no pattern exists yet)

#### Example 1

````
### [BSMP-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Source Pattern Identification**: [SAP-<CATEGORY>-<NUMBER>]

- **Source Example**:
```c
<SOURCE CODE EXAMPLE>
```

- **Build Requirement**: <BUILD REQUIREMENT DESCRIPTION>

- **Bazel Mapping Description**: <BAZEL MAPPING DESCRIPTION>

- **Bazel Code Example**:
```starlark
<BAZEL CODE EXAMPLE>
```

- **Output Bazel File**: <BAZEL FILE TYPE>

- **Exceptional Cases**: None.
````

#### Example 2

````
### [BSMP-<CATEGORY>-<NUMBER>] <TITLE>
- **Pattern Description**: <DESCRIPTION>

- **Source Pattern Identification**: [SAP-<CATEGORY>-<NUMBER>]

- **Source Example**:
```c
<SOURCE CODE EXAMPLE>
```

- **Build Requirement**: <BUILD REQUIREMENT DESCRIPTION>

- **Bazel Mapping Description**: <BAZEL MAPPING DESCRIPTION>

- **Bazel Code Example**:
```starlark
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
```c
<SOURCE CODE EXAMPLE>
```
  - **Exception Build Requirement**: <EXCEPTION BUILD REQUIREMENT DESCRIPTION>
  - **Exception Bazel Mapping**: <EXCEPTION BAZEL MAPPING DESCRIPTION>
  - **Exception Handler Reference**: [BSMP-<CATEGORY>-<NUMBER>]
````
