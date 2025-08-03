# Assess Source Code for Build System Requirements

This document provides instructions for analyzing C and C++ source code files to identify **build system requirements only**. The goal is to create focused assessments that document specific build system needs such as compiler flags, library dependencies, linking requirements, and compilation constraints.

**IMPORTANT**: Only analyze source code constructs that impose specific build system requirements. Do not document standard C/C++ language features, internal data structures, business logic, or constructs that require no special build system configuration.

For every C/C++ source code file present:
1. Read the existing `source-assessment-runbook.md` to understand patterns and approaches for interpreting C/C++ source code build characteristics.
2. Assess the source code first to understand its build dependencies, compilation requirements, and external interfaces.
3. For each source code file: create a `<file name>.source-assessment.md` file that provides a complete analysis of the source code's build-relevant characteristics.

## Source Assessment (`<file name>.source-assessment.md`)

The source assessment documents what build-relevant characteristics the source code exhibits and what build requirements it imposes. This serves as a comprehensive record of the source code's compilation dependencies, interface definitions, and build constraints that can be referenced during build system creation or maintenance activities.

### Source Assessment Guidelines
* Follow the structure and sections defined in `source-assessment-runbook.md`
* **Only analyze constructs that impose specific build system requirements**
* Skip standard C/C++ language features that require no special build configuration
* Focus on compilation dependencies, linking requirements, and build-time configuration options
* Assess at the compilation unit level, explaining each build system requirement
* Maintain consistent formatting that mirrors the section structure in the assessment runbook
* **Do not document**: business logic, internal data structures, standard language constructs, or anything that compiles without special build system configuration

### Source Assessment Contents
* For each assessment point, include:
  - **Reference Tag**: uniquely identify the source assessment point (e.g., `[SA-<CATEGORY>-<NUMBER>]`)
  - **Description**: explain what this specific C/C++ source code construct means for build requirements and how it affects compilation
  - **Source Code Snippet**: relevant source code that demonstrates this assessment point
  - **Line Number in Source File**: specific line number or range where this construct appears
  - **Build Requirement**: describe the specific build system configuration needed
  - **Bazel Mapping Description**: Leave blank; this will be filled in later
  - **Bazel Code Snippet**: Leave blank; this will be filled in later
  - **Output Bazel File**: Leave blank; this will be filled in later
  - **References**: Reference to all relevant assessment pattern tags (e.g., `[SAP-<CATEGORY>-<NUMBER>]`). If no relevant assessment pattern exists in the runbook, write `TODO`; a later stage will fill this in.

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
- **Bazel Mapping Description**: 
- **Bazel Code Snippet**:
- **Output Bazel File**:
- **References**: [SAP-<CATEGORY>-<NUMBER>]
````
