# Bazel Mapping for Build Assessments

This document provides instructions for mapping existing build system constructs to their Bazel equivalents. The goal is to fill in the Bazel-related fields in the build assessment files created in Stage 1, using the patterns documented in the build assessment runbook.

**Important**: This stage only fills in the blank Bazel-related fields from previous stages. Do not create actual Bazel build files - only complete the assessment documentation.

For every build assessment file created in Stage 1:
1. Read the existing `bazel-mapping-runbook.<build system>.md` to understand patterns and approaches for converting build system constructs to Bazel equivalents.
2. For each `<file name>.build-assessment.md`:
   - Fill in the **Bazel Mapping Description** field for each assessment point
   - Fill in the **Bazel Code Snippet** field for each assessment point
   - Fill in the **Output Bazel File** field for each assessment point
   - Leave all other fields unchanged

## Bazel Mapping Guidelines

The Bazel mapping process translates existing build system constructs into equivalent Bazel build rules and configurations. This creates a direct correspondence between what the original build system accomplishes and how Bazel would accomplish the same goals.

### Bazel Mapping Process
* Follow the structure and mapping patterns defined in `bazel-mapping-runbook.<build system>.md`
* Map each build assessment point individually to its corresponding Bazel equivalent
* Focus on functional equivalence - the Bazel mapping should accomplish the same build goals as the original construct
* Use appropriate Bazel rules, attributes, and conventions that match the original build system's intent
* Maintain consistency with Bazel best practices and idioms
* Ensure mapped Bazel constructs integrate properly with other build targets and dependencies

### Bazel Mapping Contents
* For each build assessment point, fill in:
  - **Bazel Mapping Description**: Explain how the original build construct maps to Bazel, describing which Bazel rules, attributes, or mechanisms accomplish the same goals
  - **Bazel Code Snippet**: Provide the actual Bazel BUILD.bazel file syntax that implements this mapping
  - **Output Bazel File**: Specify the relative path to the Bazel file where this construct should be placed. Most build targets go in BUILD.bazel files (e.g., `BUILD.bazel`, `src/BUILD.bazel`, `lib/BUILD.bazel`), but some constructs may belong in other Bazel files such as MODULE.bazel for module definitions or .bzl files for custom rules and macros.
  - **References**: Update to include references to all relevant build assessment pattern tags (e.g., `[BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`) and bazel mapping pattern tags (e.g., `[BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`). If no relevant bazel mapping pattern exists in the runbook, write `TODO` for the bazel mapping reference while preserving existing build assessment pattern references.

#### Example

````
### [BA-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>] <TITLE>
- **Description**: <DESCRIPTION>

- **Source Code Snippet**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Path to Source File**: <RELATIVE FILE PATH>
- **Line Number in Source File**: <LINE NUMBER>
- **Bazel Mapping Description**: <BAZEL MAPPING DESCRIPTION>
- **Bazel Code Snippet**:
```<LANGUAGE>
<BAZEL CODE EXAMPLE>
```
- **Output Bazel File**: <BAZEL FILE PATH>
- **References**: [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>], [BMP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]
````
