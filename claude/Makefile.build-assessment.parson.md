# Parson Makefile Build Assessment and Bazel Mapping

- **Source Code Snippet**:
```makefile
CC = gcc
CFLAGS = -O0 -g -Wall -Wextra -std=c89 -pedantic-errors -DTESTS_MAIN
```

- **Description**: Defines the C compiler as gcc and sets compilation flags including optimization level (-O0), debug symbols (-g), warning flags (-Wall -Wextra), C89 standard compliance (-std=c89), strict error checking (-pedantic-errors), and a preprocessor definition (-DTESTS_MAIN).
- **Bazel Mapping**:
  - **Description**: Compiler and compilation flags are specified through cc_library/cc_binary copts attribute and toolchain configuration. The TESTS_MAIN definition can be handled through defines attribute.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    load("@rules_cc//cc:defs.bzl", "cc_binary", "cc_library")
    
    # Common compilation flags for C targets
    COMMON_COPTS = [
        "-O0",
        "-g", 
        "-Wall",
        "-Wextra",
        "-std=c89",
        "-pedantic-errors",
    ]
    
    # Common defines for test targets
    COMMON_DEFINES = ["TESTS_MAIN"]
    ```

---

- **Source Code Snippet**:
```makefile
CPPC = g++
CPPFLAGS = -O0 -g -Wall -Wextra -DTESTS_MAIN
```

- **Description**: Defines the C++ compiler as g++ and sets compilation flags similar to the C flags but without the C89 standard specification, allowing for C++ compilation of the same source files.
- **Bazel Mapping**:
  - **Description**: C++ compilation flags are specified through cc_binary/cc_library copts attribute, with Bazel automatically selecting appropriate toolchain based on file extensions and target type.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    # Common compilation flags for C++ targets
    COMMON_CPP_COPTS = [
        "-O0",
        "-g",
        "-Wall", 
        "-Wextra",
    ]
    ```

---

- **Source Code Snippet**:
```makefile
all: test testcpp test_hash_collisions
```

- **Description**: Defines the default target "all" that depends on three test executables: test, testcpp, and test_hash_collisions. When make is run without arguments, it will build all three targets.
- **Bazel Mapping**:
  - **Description**: Bazel doesn't have a direct equivalent to "all" targets, but similar functionality can be achieved using test_suite to group related targets or by specifying multiple targets on the command line.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
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

---

- **Source Code Snippet**:
```makefile
.PHONY: test testcpp test_hash_collisions
```

- **Description**: Declares the three targets as phony targets, meaning they don't correspond to actual files but are command sequences to be executed. This prevents make from getting confused if files with these names exist.
- **Bazel Mapping**: N/A. Bazel rules inherently understand the difference between file targets and executable targets. The concept of phony targets doesn't apply since Bazel targets are explicitly typed (cc_binary, cc_test, etc.) and don't conflict with filesystem artifacts.

---

- **Source Code Snippet**:
```makefile
test: tests.c parson.c
	$(CC) $(CFLAGS) -o $@ tests.c parson.c
	./$@
```

- **Description**: Creates a test executable by compiling tests.c and parson.c with the C compiler and flags, then immediately executes the resulting binary. This combines both building and running the test in a single target.
- **Bazel Mapping**:
  - **Description**: Separate the build and execution concerns using cc_test rule for building the test executable, which Bazel can then run with 'bazel test'. The automatic execution is handled by Bazel's test runner.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_test(
        name = "test",
        srcs = [
            "tests.c",
            "parson.c",
            "parson.h",
        ],
        copts = COMMON_COPTS,
        defines = COMMON_DEFINES,
    )
    ```

---

- **Source Code Snippet**:
```makefile
testcpp: tests.c parson.c
	$(CPPC) $(CPPFLAGS) -o $@ tests.c parson.c
	./$@
```

- **Description**: Creates a C++ test executable by compiling the same C source files (tests.c and parson.c) with the C++ compiler to verify C++ compatibility, then executes the resulting binary.
- **Bazel Mapping**:
  - **Description**: Use cc_test rule but ensure C++ compilation by either renaming sources to .cpp or using appropriate compiler flags. Bazel will automatically use C++ toolchain based on file extensions or explicit configuration.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_test(
        name = "testcpp",
        srcs = [
            "tests.c",
            "parson.c", 
            "parson.h",
        ],
        copts = COMMON_CPP_COPTS + ["-x", "c++"],
        defines = COMMON_DEFINES,
    )
    ```

---

- **Source Code Snippet**:
```makefile
test_hash_collisions: tests.c parson.c
	$(CC) $(CFLAGS) -DPARSON_FORCE_HASH_COLLISIONS -o $@ tests.c parson.c
	./$@
```

- **Description**: Creates a specialized test executable that adds the PARSON_FORCE_HASH_COLLISIONS preprocessor definition to test hash collision scenarios in the parson library, then executes the binary.
- **Bazel Mapping**:
  - **Description**: Use cc_test rule with an additional preprocessor definition in the defines attribute to enable hash collision testing mode.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_test(
        name = "test_hash_collisions",
        srcs = [
            "tests.c",
            "parson.c",
            "parson.h",
        ],
        copts = COMMON_COPTS,
        defines = COMMON_DEFINES + ["PARSON_FORCE_HASH_COLLISIONS"],
    )
    ```

---

- **Source Code Snippet**:
```makefile
clean:
	rm -f test *.o
```

- **Description**: Provides a clean target that removes built executables (test) and object files (*.o) to reset the build environment. This is a maintenance target for cleaning up build artifacts.
- **Bazel Mapping**: N/A. Bazel handles cleaning through the 'bazel clean' command which removes all build artifacts from the bazel-* directories. Bazel's hermetic build approach means individual file cleanup targets are not needed, as all build artifacts are managed by Bazel in its output directories.

---

- **Source Code Snippet**:
```makefile
CC = gcc
CPPC = g++
```

- **Description**: Explicit compiler selection allowing the build system to use specific compiler executables rather than relying on system defaults. This provides control over which compiler version and type is used.
- **Bazel Mapping**: N/A. Bazel handles compiler selection through toolchain configuration and platform definitions rather than explicit compiler variable assignments. The specific compiler is determined by the registered toolchains and can be configured through toolchain registration and platform constraints.
