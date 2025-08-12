# Build Assessment and Bazel Mapping

Analyze the provided build script and produce a detailed assessment file named `<file name>.build-assessment.md` that maps each significant build construct to its Bazel equivalent. This is a mapping exercise, not a migration - focus on documenting relationships between the two build systems rather than producing a complete Bazel implementation. You are done when you have identified and mapped all significant build constructs, providing clear explanations of how each would be implemented in Bazel.

## Guidelines
* Produce the assessment file with the name pattern `<file name>.build-assessment.md` and write the complete assessment analysis to that file
* Analyze each logical build step/target by understanding what the build system accomplishes, how it accomplishes it, and explaining how Bazel would achieve the same goals through its equivalent constructs
* Map each build construct to its functional Bazel equivalent, ensuring the mapping accomplishes the same build goals
* Separate each assessment point in the Contents section with a markdown horizontal rule (three hyphens: ---) for visual clarity
* Use version placeholders for external dependencies and toolchain versions in Bazel mapping items (e.g., rules_cc version, Bazel version in .bazelversion files, third-party dependency versions). Example placeholders: <BAZEL_VERSION>, <RULES_CC_VERSION>, <PROTOBUF_VERSION>. Use actual version numbers when they refer to the source project's own version. External dependency versions will be filled in later during implementation.

## Contents
* For each assessment point, include:
  - **Source Code Snippet**: relevant source code that demonstrates this assessment point
  - **Description**: explain what this specific build construct accomplishes and how it works
  - **Bazel Mapping**: List of mapping items, where each item includes:
    - **Description**: explain how the original build construct maps to Bazel, describing which Bazel rules, attributes, or mechanisms accomplish the same goals
    - **Output Bazel File**: relative path from the workspace root where this Bazel code should be placed (e.g., BUILD.bazel, MODULE.bazel, tools/my_rule.bzl)
    - **Code Snippet**: provide the actual Bazel BUILD.bazel, MODULE.bazel, or starlark file syntax that implements this mapping
    - Use "N/A" if the source item does not map to a Bazel equivalent, include an explanation of why there's no mapping, and omit individual mapping items

### Example

````
- **Source Code Snippet**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE>
```

- **Description**: <DESCRIPTION>
- **Bazel Mapping**:
  - **Description**: <DESCRIPTION 1>
  - **Output Bazel File**: <RELATIVE_PATH_1>
  - **Code Snippet**:
    ```<LANGUAGE>
    <BAZEL CODE EXAMPLE 1>
    ```
  - **Description**: <DESCRIPTION 2>
  - **Output Bazel File**: <RELATIVE_PATH_2>
  - **Code Snippet**:
    ```<LANGUAGE>
    <BAZEL CODE EXAMPLE 2>
    ```

---

- **Source Code Snippet**:
```<LANGUAGE>
<SOURCE CODE EXAMPLE WITH NO BAZEL EQUIVALENT>
```

- **Description**: <DESCRIPTION OF NON-MAPPABLE CONSTRUCT>
- **Bazel Mapping**: N/A. <EXPLANATION OF WHY THERE'S NO BAZEL EQUIVALENT>
````
