# Makefile Bazel Mapping Runbook Proposals

This document proposes new bazel mapping patterns based on the Makefile build assessment for the parson library project.

## Proposed Bazel Mapping Patterns

### [PROPOSED-BMP-MAKE-VARIABLE-001] Compiler Selection Variable to Bazel Toolchain
- **Pattern Description**: Maps Makefile compiler selection variables (CC, CPPC) to Bazel's toolchain system, where compiler selection is handled automatically or through toolchain configuration.

- **Build System Pattern Identification**: [PROPOSED-BAP-MAKE-VARIABLE-002] Compiler Selection Variable

- **Source Example**:
```makefile
CC = gcc
CPPC = g++
```

- **Bazel Mapping Description**: Bazel handles compiler selection through its toolchain system. The default C/C++ compiler is automatically selected based on the platform, or can be overridden using --crosstool_top flag or custom toolchain definitions. No explicit mapping is needed in BUILD.bazel files.

- **Bazel Code Example**:
```starlark
# No explicit compiler selection needed in BUILD.bazel
# Handled by Bazel's toolchain system automatically
```

- **Output Bazel File**: N/A (handled by Bazel's rule system)

- **Exceptional Cases**: None.

### [PROPOSED-BMP-MAKE-VARIABLE-002] Compiler Flags Variable to Bazel Attributes
- **Pattern Description**: Maps Makefile compiler flags variables (CFLAGS, CPPFLAGS) to Bazel's copts and defines attributes in cc_binary/cc_library/cc_test rules.

- **Build System Pattern Identification**: [PROPOSED-BAP-MAKE-VARIABLE-003] Compiler Flags Variable

- **Source Example**:
```makefile
CFLAGS = -O0 -g -Wall -Wextra -std=c89 -pedantic-errors -DTEST_MODE
CPPFLAGS = -O0 -g -Wall -Wextra -DTEST_MODE
```

- **Bazel Mapping Description**: Bazel compiler flags are specified using the copts attribute for compilation options and the defines attribute for preprocessor definitions. Debug and optimization flags can also be controlled through compilation_mode setting.

- **Bazel Code Example**:
```starlark
CFLAGS = ["-Wall", "-Wextra", "-std=c89", "-pedantic-errors"]
DEFINES = ["TEST_MODE"]

cc_library(
    name = "example",
    srcs = ["example.c"],
    copts = CFLAGS,
    defines = DEFINES,
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-MAKE-TARGET-001] Default All Target to Bazel Test Suite
- **Pattern Description**: Maps Makefile's default "all" target that builds multiple test executables to Bazel's test_suite rule for grouping related test targets.

- **Build System Pattern Identification**: [PROPOSED-BAP-MAKE-TARGET-005] Default Target

- **Source Example**:
```makefile
all: test testcpp test_specialized
```

- **Bazel Mapping Description**: Bazel uses test_suite rule to group multiple test targets together. This allows running all related tests with a single target, similar to the "all" target behavior in Makefiles.

- **Bazel Code Example**:
```starlark
test_suite(
    name = "all_tests",
    tests = [
        ":test",
        ":testcpp", 
        ":test_specialized",
    ],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-MAKE-TARGET-002] Test Target with Execution to Bazel Test Rule
- **Pattern Description**: Maps Makefile test targets that compile source files and immediately execute the resulting binary to Bazel's cc_test rule, which handles both compilation and execution through Bazel's test infrastructure.

- **Build System Pattern Identification**: [PROPOSED-BAP-MAKE-TARGET-003] Test Execution Target

- **Source Example**:
```makefile
test: test_sources.c library.c
	$(CC) $(CFLAGS) -o $@ test_sources.c library.c
	./$@
```

- **Bazel Mapping Description**: Bazel uses cc_test rule to create a test executable that is automatically run when the test target is executed. The compilation and execution are handled by Bazel's test infrastructure, eliminating the need for separate compile and run steps.

- **Bazel Code Example**:
```starlark
cc_test(
    name = "test",
    srcs = ["test_sources.c", "library.c", "library.h"],
    copts = CFLAGS,
    defines = DEFINES,
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-MAKE-TARGET-003] Clean Target to Bazel Clean Command
- **Pattern Description**: Maps Makefile clean targets that remove build artifacts to Bazel's built-in clean command functionality, which is handled at the command-line level rather than in BUILD files.

- **Build System Pattern Identification**: [PROPOSED-BAP-MAKE-TARGET-004] Clean Target

- **Source Example**:
```makefile
clean:
	rm -f test *.o
```

- **Bazel Mapping Description**: Bazel provides built-in clean functionality through the "bazel clean" command. No explicit clean target needs to be defined in BUILD.bazel files as Bazel manages its own build artifacts and output directories.

- **Bazel Code Example**:
```bash
# Bazel clean is handled by the bazel command:
bazel clean
# No BUILD.bazel equivalent needed
```

- **Output Bazel File**: N/A (handled by bazel command-line)

- **Exceptional Cases**: None.

### [PROPOSED-BMP-MAKE-PHONY-001] Phony Target Declaration to Implicit Bazel Target Types
- **Pattern Description**: Maps Makefile .PHONY declarations to Bazel's implicit target type system, where targets are inherently action-based rather than file-based depending on their rule type.

- **Build System Pattern Identification**: [PROPOSED-BAP-MAKE-PHONY-001] Phony Target Declaration

- **Source Example**:
```makefile
.PHONY: test testcpp test_specialized
```

- **Bazel Mapping Description**: Bazel targets are inherently "phony" or action-based depending on their rule type. No explicit phony declaration is needed as Bazel handles target types automatically based on the rule (cc_test, cc_binary, etc.).

- **Bazel Code Example**:
```starlark
# No explicit phony declaration needed in Bazel
# cc_test rules are inherently action-based targets
```

- **Output Bazel File**: N/A (implicit in Bazel's target system)

- **Exceptional Cases**: None.
