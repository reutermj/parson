# Bazel Source Mapping Runbook Proposals: tests.c

This document proposes new bazel source mapping patterns based on the analysis of `tests.c` during the Bazel source mapping process.

## Proposed Bazel Source Mapping Patterns

### [PROPOSED-BSMP-COMPILER-002] Apple Clang Pragma Directives
- **Pattern Description**: When C/C++ source code uses compiler-specific pragma directives (like Apple Clang deprecation warning suppression), no special Bazel configuration is needed as the pragma is embedded in the source code.

- **Source Pattern Identification**: [PROPOSED-SAP-COMPILER-002]

- **Source Example**:
```c
#if defined(__APPLE__) && defined(__clang__)
    #pragma clang diagnostic ignored "-Wdeprecated-declarations"
#endif
```

- **Build Requirement**: Compiler support for pragma directives, no special build flags needed

- **Bazel Mapping Description**: Compiler-specific pragma directives are handled automatically by the source code when compiled with the appropriate compiler. No special Bazel configuration needed as the directive is embedded in the source.

- **Bazel Code Example**:
```starlark
# No specific Bazel code needed - handled by source code pragma directive
```

- **Output Bazel File**: N/A

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-TEST-001] Test Target Library Dependencies
- **Pattern Description**: When test files include library headers they're testing, the test target depends on the library through the deps attribute to provide access to headers and ensure proper compilation dependencies.

- **Source Pattern Identification**: [PROPOSED-SAP-DEPS-001]

- **Source Example**:
```c
#include "library.h"
```

- **Build Requirement**: The library header file must be available in the include path during test compilation

- **Bazel Mapping Description**: The test target depends on the library through the deps attribute, which provides access to the library header and ensures proper compilation dependencies.

- **Bazel Code Example**:
```starlark
cc_test(
    name = "library_test",
    srcs = ["tests.c"],
    deps = [":library"],
    defines = ["TESTS_MAIN"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-TEST-002] Test Math Library Dependencies
- **Pattern Description**: When test code uses mathematical functions that may require linking with the math library, this is handled using select() to conditionally link -lm on Unix-like systems.

- **Source Pattern Identification**: [PROPOSED-SAP-DEPS-002]

- **Source Example**:
```c
#include <math.h>

#define DBL_EQ(a, b) (fabs((a) - (b)) < DBL_EPSILON)
```

- **Build Requirement**: Standard C library must be available during compilation and linking. May require linking with math library (-lm) on some systems for mathematical test functions

- **Bazel Mapping Description**: Standard C library headers are automatically available in Bazel. Math library dependency is handled using select() to conditionally link -lm on Unix-like systems.

- **Bazel Code Example**:
```starlark
cc_test(
    name = "library_test",
    srcs = ["tests.c"],
    deps = [":library"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "//conditions:default": [],
    }),
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-EXECUTABLE-001] Conditional Main Function for Tests
- **Pattern Description**: When test files include a main function conditionally compiled based on a preprocessor define, this is enabled in Bazel using the defines attribute of the cc_test rule.

- **Source Pattern Identification**: [PROPOSED-SAP-EXECUTABLE-001]

- **Source Example**:
```c
#ifdef TESTS_MAIN
int main(int argc, char *argv[]) {
    // test execution code
}
#endif
```

- **Build Requirement**: To build as standalone executable, define the conditional symbol during compilation. Build system must support conditional compilation and executable targets

- **Bazel Mapping Description**: The conditional main function is enabled by defining the required symbol in the cc_test rule's defines attribute. Bazel's cc_test rule automatically creates an executable test target.

- **Bazel Code Example**:
```starlark
cc_test(
    name = "library_test",
    srcs = ["tests.c"],
    deps = [":library"],
    defines = ["TESTS_MAIN"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-FILESYSTEM-001] Test Data File Access
- **Pattern Description**: When test code requires access to external test data files, these are provided through the data attribute of the cc_test rule to ensure availability in the test's runfiles.

- **Source Pattern Identification**: [PROPOSED-SAP-FILESYSTEM-001]

- **Source Example**:
```c
static const char *g_tests_path = "tests";

static char * read_file(const char * filename);
const char* get_file_path(const char *filename);
```

- **Build Requirement**: Test execution requires access to test data files in a specific directory. Build system must ensure test files are available at runtime

- **Bazel Mapping Description**: Test data files are provided to the test through the data attribute of the cc_test rule, ensuring they are available in the test's runfiles at runtime.

- **Bazel Code Example**:
```starlark
cc_test(
    name = "library_test",
    srcs = ["tests.c"],
    deps = [":library"],
    data = glob(["tests/*.txt"]),
    defines = ["TESTS_MAIN"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-FILESYSTEM-002] Test File Writing with Bazel Sandboxing
- **Pattern Description**: When test code writes temporary files during execution, Bazel requires these files to be written to the directory specified by the $TEST_TMPDIR environment variable, necessitating source code modifications.

- **Source Pattern Identification**: [PROPOSED-SAP-FILESYSTEM-002]

- **Source Example**:
```c
// Original code writing to current directory
void test_function(void) {
    const char *temp_filename = "test_output.txt";
    write_file(temp_filename, data);
    remove(temp_filename);
}
```

- **Build Requirement**: Test execution requires write access to a directory for creating temporary files. Standard library functions must be available for file operations

- **Bazel Mapping Description**: Bazel tests must write files to the directory specified by the $TEST_TMPDIR environment variable, not to the source tree. This requires source code modifications to use $TEST_TMPDIR for file creation instead of writing to the current directory.

- **Bazel Code Example**:
```c
// Modified C source code for Bazel $TEST_TMPDIR compatibility:

static const char* get_temp_dir(void) {
    const char *tmpdir = getenv("TEST_TMPDIR");
    return tmpdir ? tmpdir : ".";
}

void test_function(void) {
    char temp_path[512];
    const char *tmpdir = get_temp_dir();
    snprintf(temp_path, sizeof(temp_path), "%s/test_output.txt", tmpdir);
    write_file(temp_path, data);
    remove(temp_path);
}
```

- **Output Bazel File**: tests.c (source code modifications)

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-TESTING-001] Custom Memory Allocation Testing
- **Pattern Description**: When test code implements custom memory allocation functions for testing memory behavior, these rely on standard library functions that are automatically available in Bazel.

- **Source Pattern Identification**: [PROPOSED-SAP-TESTING-001]

- **Source Example**:
```c
static int g_malloc_count = 0;
static void *counted_malloc(size_t size);
static void counted_free(void *ptr);

typedef struct failing_alloc {
    int allocation_to_fail;
    int should_fail;
} failing_alloc_t;
```

- **Build Requirement**: Standard library memory allocation functions must be available for override. No special build system configuration required

- **Bazel Mapping Description**: Standard library memory allocation functions are automatically available in Bazel C compilation. The custom memory allocation testing functionality is handled entirely by the test source code.

- **Bazel Code Example**:
```starlark
cc_test(
    name = "library_test",
    srcs = ["tests.c"],
    deps = [":library"],
    # Standard library automatically available
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.
