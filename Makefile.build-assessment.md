# Makefile Build Assessment

This document provides a comprehensive analysis of the Makefile build system for the parson library project.

## Project Overview
The Makefile build system creates test executables for the parson library with different configurations and compiler options for testing purposes.

## Variable Configuration

### [BA-MAKE-VARIABLE-001] C Compiler Variable Definition
- **Description**: Defines the C compiler to use (gcc) for building C source files. This variable can be overridden from the command line or environment.

- **Source Code Snippet**:
```makefile
CC = gcc
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 1
- **Bazel Mapping Description**: Bazel handles compiler selection through toolchain configuration. The default C compiler is automatically selected based on the platform, or can be overridden using --crosstool_top flag or custom toolchain definitions.
- **Bazel Code Snippet**:
```starlark
# Compiler selection is handled by Bazel's toolchain system
# No explicit mapping needed in BUILD.bazel file
```
- **Output Bazel File**: N/A (handled by Bazel's rule system)
- **References**: [PROPOSED-BAP-MAKE-VARIABLE-002] Compiler Selection Variable, [TODO] Bazel Compiler Selection

### [BA-MAKE-VARIABLE-002] C Compiler Flags Variable Definition
- **Description**: Defines compilation flags for C source files including debug information (-g), no optimization (-O0), warning flags (-Wall -Wextra), C89 standard compliance (-std=c89), pedantic error checking (-pedantic-errors), and a preprocessor definition for test mode (-DTESTS_MAIN).

- **Source Code Snippet**:
```makefile
CFLAGS = -O0 -g -Wall -Wextra -std=c89 -pedantic-errors -DTESTS_MAIN
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 2
- **Bazel Mapping Description**: Bazel compilation flags are specified using copts attribute in cc_binary/cc_library rules, and defines attribute for preprocessor definitions. Debug and optimization flags can be controlled through compilation_mode.
- **Bazel Code Snippet**:
```starlark
CFLAGS = ["-Wall", "-Wextra", "-std=c89", "-pedantic-errors"]
DEFINES = ["TESTS_MAIN"]

# Used in cc_library/cc_binary rules:
# copts = CFLAGS,
# defines = DEFINES,
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-MAKE-VARIABLE-003] Compiler Flags Variable, [TODO] Bazel Compiler Flags

### [BA-MAKE-VARIABLE-003] C++ Compiler Variable Definition
- **Description**: Defines the C++ compiler to use (g++) for building C++ source files or testing C code with C++ compiler compatibility.

- **Source Code Snippet**:
```makefile
CPPC = g++
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 4
- **Bazel Mapping Description**: Bazel handles C++ compiler selection through toolchain configuration, similar to C compiler selection. C++ compilation is enabled by using cc_binary/cc_library rules with C++ source files.
- **Bazel Code Snippet**:
```starlark
# C++ compiler selection is handled by Bazel's toolchain system
# Use cc_binary/cc_library for C++ targets
```
- **Output Bazel File**: N/A (handled by Bazel's rule system)
- **References**: [PROPOSED-BAP-MAKE-VARIABLE-002] Compiler Selection Variable, [TODO] Bazel C++ Compiler Selection

### [BA-MAKE-VARIABLE-004] C++ Compiler Flags Variable Definition
- **Description**: Defines compilation flags for C++ compilation including debug information (-g), no optimization (-O0), warning flags (-Wall -Wextra), and a preprocessor definition for test mode (-DTESTS_MAIN).

- **Source Code Snippet**:
```makefile
CPPFLAGS = -O0 -g -Wall -Wextra -DTESTS_MAIN
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 5
- **Bazel Mapping Description**: C++ compilation flags in Bazel are specified using copts attribute in cc_binary/cc_library rules for C++ targets, with defines attribute for preprocessor definitions.
- **Bazel Code Snippet**:
```starlark
CPPFLAGS = ["-Wall", "-Wextra"]
CPP_DEFINES = ["TESTS_MAIN"]

# Used in cc_binary/cc_test rules:
# copts = CPPFLAGS,
# defines = CPP_DEFINES,
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-MAKE-VARIABLE-003] Compiler Flags Variable, [TODO] Bazel C++ Compiler Flags

## Target Configuration

### [BA-MAKE-TARGET-001] All Target Definition
- **Description**: Defines the default target that builds all test executables when make is run without arguments. This target depends on test, testcpp, and test_hash_collisions targets.

- **Source Code Snippet**:
```makefile
all: test testcpp test_hash_collisions
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 7
- **Bazel Mapping Description**: Bazel uses test_suite rule to group multiple test targets together. The default target behavior can be achieved by specifying targets in .bazelrc or creating an alias.
- **Bazel Code Snippet**:
```starlark
test_suite(
    name = "all_tests",
    tests = [
        ":test",
        ":testcpp", 
        ":test_hash_collisions",
    ],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-MAKE-TARGET-005] Default Target, [TODO] Bazel Test Suite

### [BA-MAKE-TARGET-002] Test Target with C Compiler
- **Description**: Defines the test target that compiles tests.c and parson.c with the C compiler using standard flags, creates an executable named 'test', and immediately runs it. This target builds and executes the standard C test suite.

- **Source Code Snippet**:
```makefile
test: tests.c parson.c
	$(CC) $(CFLAGS) -o $@ tests.c parson.c
	./$@
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 10-12
- **Bazel Mapping Description**: Bazel uses cc_test rule to create a test executable that is automatically run when the test target is executed. The compilation and execution are handled by Bazel's test infrastructure.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "test",
    srcs = ["tests.c", "parson.c", "parson.h"],
    copts = CFLAGS,
    defines = DEFINES,
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-MAKE-TARGET-003] Test Execution Target, [TODO] Bazel C Test Target

### [BA-MAKE-TARGET-003] Test Target with C++ Compiler
- **Description**: Defines the testcpp target that compiles tests.c and parson.c with the C++ compiler using C++ flags, creates an executable named 'testcpp', and immediately runs it. This target tests C++ compatibility of the C library.

- **Source Code Snippet**:
```makefile
testcpp: tests.c parson.c
	$(CPPC) $(CPPFLAGS) -o $@ tests.c parson.c
	./$@
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 14-16
- **Bazel Mapping Description**: Bazel uses cc_test rule with C++ compiler to test C code compatibility with C++. The .c files will be compiled as C++ when using cc_test.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "testcpp",
    srcs = ["tests.c", "parson.c"],
    hdrs = ["parson.h"],
    copts = CPPFLAGS,
    defines = CPP_DEFINES,
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-MAKE-TARGET-003] Test Execution Target, [TODO] Bazel C++ Compatibility Test

### [BA-MAKE-TARGET-004] Hash Collision Test Target
- **Description**: Defines the test_hash_collisions target that compiles tests.c and parson.c with the C compiler using standard flags plus an additional preprocessor definition (-DPARSON_FORCE_HASH_COLLISIONS), creates an executable named 'test_hash_collisions', and immediately runs it. This target tests the library's behavior under hash collision conditions.

- **Source Code Snippet**:
```makefile
test_hash_collisions: tests.c parson.c
	$(CC) $(CFLAGS) -DPARSON_FORCE_HASH_COLLISIONS -o $@ tests.c parson.c
	./$@
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 18-20
- **Bazel Mapping Description**: Bazel uses cc_test rule with additional preprocessor definitions in the defines attribute to test specific library behaviors like hash collision handling.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "test_hash_collisions",
    srcs = ["tests.c", "parson.c", "parson.h"],
    copts = CFLAGS,
    defines = DEFINES + ["PARSON_FORCE_HASH_COLLISIONS"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-MAKE-TARGET-003] Test Execution Target, [TODO] Bazel Conditional Test Defines

### [BA-MAKE-TARGET-005] Clean Target Definition
- **Description**: Defines the clean target that removes generated files including the test executable and any object files (*.o) to provide a clean build environment.

- **Source Code Snippet**:
```makefile
clean:
	rm -f test *.o
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 22-23
- **Bazel Mapping Description**: Bazel provides built-in clean functionality through "bazel clean" command. No explicit clean target needs to be defined in BUILD.bazel files as Bazel manages its own build artifacts.
- **Bazel Code Snippet**:
```bash
# Bazel clean is handled by the bazel command:
# bazel clean
# No BUILD.bazel equivalent needed
```
- **Output Bazel File**: N/A (handled by bazel command-line)
- **References**: [PROPOSED-BAP-MAKE-TARGET-004] Clean Target, [TODO] Bazel Clean Command

## Phony Target Configuration

### [BA-MAKE-PHONY-001] Phony Targets Declaration
- **Description**: Declares test, testcpp, and test_hash_collisions as phony targets, meaning they don't represent actual files but are action targets that should always be executed when requested.

- **Source Code Snippet**:
```makefile
.PHONY: test testcpp test_hash_collisions
```

- **Path to Source File**: Makefile
- **Line Number in Source File**: 9
- **Bazel Mapping Description**: Bazel targets are inherently "phony" - they represent actions rather than files. No explicit phony declaration is needed as Bazel handles target types automatically based on the rule type (cc_test, cc_binary, etc.).
- **Bazel Code Snippet**:
```starlark
# No explicit phony declaration needed in Bazel
# cc_test rules are inherently action-based targets
```
- **Output Bazel File**: N/A (implicit in Bazel's target system)
- **References**: [PROPOSED-BAP-MAKE-PHONY-001] Phony Target Declaration, [TODO] Bazel Target Types
