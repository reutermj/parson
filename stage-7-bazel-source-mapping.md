# Bazel Mapping for Source Assessments

This document provides instructions for mapping C/C++ source code constructs to their Bazel equivalents. The goal is to fill in the Bazel-related fields in the source assessment files created in Stage 3, using the patterns documented in the source assessment runbook.

**Important**: This stage only fills in the blank Bazel-related fields from previous stages. Do not create actual Bazel build files - only complete the assessment documentation.

For every source assessment file created in Stage 3:
1. Read the existing `bazel-source-mapping-runbook.md` to understand patterns and approaches for converting C/C++ source code constructs to Bazel equivalents.
2. For each `<file name>.source-assessment.md`:
   - Fill in the **Bazel Mapping Description** field for each assessment point
   - Fill in the **Bazel Code Snippet** field for each assessment point
   - Fill in the **Output Bazel File** field for each assessment point
   - Leave all other fields unchanged

## Bazel Source Mapping Guidelines

The Bazel source mapping process translates C/C++ source code build requirements into equivalent Bazel build rules and configurations. This creates a direct correspondence between what the source code requires from the build system and how Bazel would satisfy those requirements.

### Bazel Source Mapping Process
* Follow the structure and mapping patterns defined in `bazel-source-mapping-runbook.md`
* Map each source assessment point individually to its corresponding Bazel equivalent
* Focus on functional equivalence - the Bazel mapping should satisfy the same build requirements as identified in the source code analysis
* Use appropriate Bazel rules, attributes, and conventions that address the source code's compilation dependencies and interface requirements
* Maintain consistency with Bazel best practices and idioms
* Ensure mapped Bazel constructs integrate properly with other build targets and dependencies

### Bazel Source Mapping Contents
* For each source assessment point, fill in:
  - **Bazel Mapping Description**: Explain how the source code construct's build requirements map to Bazel, describing which Bazel rules, attributes, or mechanisms satisfy the identified build requirements
  - **Bazel Code Snippet**: Provide the actual Bazel BUILD.bazel file syntax that implements this mapping to satisfy the source code's build requirements
  - **Output Bazel File**: Specify the relative path to the Bazel file where this construct should be placed. Most build targets go in BUILD.bazel files (e.g., `BUILD.bazel`, `src/BUILD.bazel`, `lib/BUILD.bazel`), but some constructs may belong in other Bazel files such as MODULE.bazel for module definitions or .bzl files for custom rules and macros.
  - **References**: Update to include references to all relevant source assessment pattern tags (e.g., `[SAP-<CATEGORY>-<NUMBER>]`) and bazel source mapping pattern tags (e.g., `[BSMP-<CATEGORY>-<NUMBER>]`). If no relevant bazel source mapping pattern exists in the runbook, write `TODO` for the bazel source mapping reference while preserving existing source assessment pattern references.

#### Example

````
### [SA-<CATEGORY>-<NUMBER>] <TITLE>
- **Description**: <DESCRIPTION>

- **Source Code Snippet**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Path to Source File**: <RELATIVE FILE PATH>
- **Line Number in Source File**: <LINE NUMBER>
- **Build Requirement**: <BUILD REQUIREMENT DESCRIPTION>
- **Bazel Mapping Description**: <BAZEL MAPPING DESCRIPTION>
- **Bazel Code Snippet**:
```<LANGUAGE>
<BAZEL CODE EXAMPLE>
```
- **Output Bazel File**: <BAZEL FILE PATH>
- **References**: [SAP-<CATEGORY>-<NUMBER>], [BSMP-<CATEGORY>-<NUMBER>]
````
