# Assess Existing Build System

This document provides instructions for analyzing existing build systems to understand what they accomplish and how they work. The goal is to create comprehensive assessments that document the build system's intent and mechanisms.

For every build script present:
1. Read the existing `build-assessment-runbook.<build system>.md` to understand patterns and approaches for interpreting the build system.
2. Assess the existing build system first to understand its structure, dependencies, and build semantics.
3. For each build script, configuration file, or build definition: create a `<file name>.build-assessment.md` file that provides a complete analysis of the current build system.

## Build Assessment (`<file name>.build-assessment.md`)

The build assessment documents what the current build system accomplishes and how it works. This serves as a comprehensive record of the build system's intent, structure, and mechanisms that can be referenced during conversion or maintenance activities.

### Build Assessment Guidelines
* Follow the structure and sections defined in `build-assessment-runbook.<build system>.md`
* Analyze each build script, configuration file, or build definition individually to capture file-specific logic and dependencies
* Assess at the logical build step/target level, explaining each component part of the logical build step/target
* Focus on understanding what the build system is accomplishing and how it accomplishes it
* Maintain consistent formatting that mirrors the section structure in the assessment runbook

### Build Assessment Contents
* For each assessment point, include:
  - **Reference Tag**: uniquely identify the build assessment point (e.g., `[BA-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`)
  - **Description**: explain what this specific build construct accomplishes and how it works
  - **Source Code Snippet**: relevant source code that demonstrates this assessment point.
  - **Line Number in Source File**: specific line number or range where this construct appears.
  - **Bazel Mapping Description**: Leave blank; this will be filled in later
  - **Bazel Code Snippet**: Leave blank; this will be filled in later
  - **Output Bazel File**: Leave blank; this will be filled in later
  - **References**: Reference to all relevant assessment pattern tags (e.g., `[BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]`). If no relevant assessment pattern exists in the runbook, write `TODO`; a later stage will fill this in.

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
- **Bazel Mapping Description**: 
- **Bazel Code Snippet**:
- **Output Bazel File**:
- **References**: [BAP-<BUILD SYSTEM>-<CATEGORY>-<NUMBER>]
````
