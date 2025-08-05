# Source Assessment: tests.c

This document provides a complete analysis of `tests.c` build-relevant characteristics and build system requirements.

## Compiler-Specific Configurations

### [SA-COMPILER-001] MSVC Compiler Security Warning Suppression  
- **Description**: The test code defines _CRT_SECURE_NO_WARNINGS for Microsoft Visual C++ compiler to suppress security warnings about deprecated functions.

- **Source Code Snippet**:
```c
#ifdef _MSC_VER
#define _CRT_SECURE_NO_WARNINGS
#endif
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 25-27
- **Build Requirement**: When building with MSVC, this suppresses warnings. No explicit build system configuration required, but build system should allow compiler-specific defines
- **Bazel Mapping Description**: This compiler-specific define is handled automatically by the C source code itself. No special Bazel configuration is needed as the conditional compilation directive is embedded in the source code.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - handled by source code conditional compilation
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-COMPILER-001], [TODO]

### [SA-COMPILER-002] Apple Clang Deprecation Warning Suppression
- **Description**: The test code conditionally disables deprecation warnings when building with Apple Clang compiler to avoid warnings about deprecated API usage.

- **Source Code Snippet**:
```c
#if defined(__APPLE__) && defined(__clang__)
    #pragma clang diagnostic ignored "-Wdeprecated-declarations"
#endif
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 29-31
- **Build Requirement**: Apple Clang compiler support, no special build flags needed but compiler must support pragma directives
- **Bazel Mapping Description**: This compiler-specific pragma is handled automatically by the source code when compiled with Apple Clang. No special Bazel configuration needed as the directive is embedded in the source.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - handled by source code pragma directive
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-COMPILER-002], [TODO]

## Header Dependencies

### [SA-DEPS-001] Library Header Dependency
- **Description**: The test file includes the library header it's testing, creating a compilation dependency on the parson library interface.

- **Source Code Snippet**:
```c
#include "parson.h"
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 33
- **Build Requirement**: The parson.h header file must be available in the include path during compilation
- **Bazel Mapping Description**: The test target depends on the parson library through the deps attribute, which provides access to the parson.h header and ensures proper compilation dependencies.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "parson_test",
    srcs = ["tests.c"],
    deps = [":parson"],
    defines = ["TESTS_MAIN"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-DEPS-001], [TODO]

### [SA-DEPS-002] Standard C Library Testing Dependencies
- **Description**: The test implementation requires multiple standard C library headers for testing functionality including assertions, I/O, memory management, string operations, and mathematical functions.

- **Source Code Snippet**:
```c
#include <assert.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 35-39
- **Build Requirement**: Standard C library must be available during compilation and linking. May require linking with math library (-lm) on some systems for mathematical test functions
- **Bazel Mapping Description**: Standard C library headers are automatically available in Bazel. Math library dependency is handled using select() to conditionally link -lm on Unix-like systems.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "parson_test",
    srcs = ["tests.c"],
    deps = [":parson"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "//conditions:default": [],
    }),
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-DEPS-002], [TODO]

## Executable Configuration

### [SA-EXECUTABLE-001] Conditional Main Function
- **Description**: The test file includes a main function that is conditionally compiled based on the TESTS_MAIN preprocessor define, allowing the tests to be built as either a standalone executable or as part of a larger test suite.

- **Source Code Snippet**:
```c
#ifdef TESTS_MAIN
int main(int argc, char *argv[]) {
    // test execution code
}
#endif
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 102-103
- **Build Requirement**: To build as standalone executable, define TESTS_MAIN during compilation. Build system must support conditional compilation and executable targets
- **Bazel Mapping Description**: The conditional main function is enabled by defining TESTS_MAIN in the cc_test rule's defines attribute. Bazel's cc_test rule automatically creates an executable test target.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "parson_test",
    srcs = ["tests.c"],
    deps = [":parson"],
    defines = ["TESTS_MAIN"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-EXECUTABLE-001], [TODO]

## Test Framework Dependencies

### [SA-TEST-001] Custom Test Macro Framework
- **Description**: The test file defines custom testing macros that require specific compilation behavior for proper test execution reporting.

- **Source Code Snippet**:
```c
#define TEST(A) do {\
if (A) {\
    g_tests_passed++;\
} else {\
    printf("%d %-72s - FAILED\n", __LINE__, #A);\
    g_tests_failed++;\
}\
} while(0)

#define STREQ(A, B) ((A) && (B) ? strcmp((A), (B)) == 0 : 0)
#define DBL_EPSILON 2.2204460492503131e-16
#define DBL_EQ(a, b) (fabs((a) - (b)) < DBL_EPSILON)
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 41-50
- **Build Requirement**: Standard C library functions (printf, strcmp, fabs) must be available. May require math library linking for fabs function
- **Bazel Mapping Description**: The custom test macros rely on standard C library functions that are automatically available in Bazel. Math library linking for fabs is handled with conditional linkopts.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "parson_test",
    srcs = ["tests.c"],
    deps = [":parson"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "//conditions:default": [],
    }),
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-TEST-001], [TODO]

## File System Access Requirements

### [SA-FILESYSTEM-001] Test File Access
- **Description**: The test code references external test files and requires file system access for reading test data files.

- **Source Code Snippet**:
```c
static const char *g_tests_path = "tests";

static char * read_file(const char * filename);
const char* get_file_path(const char *filename);
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 74, 96-97
- **Build Requirement**: Test execution requires access to test data files in the "tests" directory. Build system must ensure test files are available at runtime
- **Bazel Mapping Description**: Test data files are provided to the test through the data attribute of the cc_test rule, ensuring they are available in the test's runfiles at runtime.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "parson_test",
    srcs = ["tests.c"],
    deps = [":parson"],
    data = glob(["tests/*.txt"]),
    defines = ["TESTS_MAIN"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-FILESYSTEM-001], [TODO]

### [SA-FILESYSTEM-002] Test File Writing Operations
- **Description**: The test code writes temporary files during test execution for serialization testing and creates files for demonstration purposes, requiring file system write access and cleanup capabilities.

- **Source Code Snippet**:
```c
// Serialization test functions that write to hardcoded filenames
void test_suite_8(void) {
    const char *temp_filename = "test_2_serialized.txt";
    JSON_Value *a = NULL, *b = NULL;
    a = json_parse_file(get_file_path(filename));
    TEST(json_serialize_to_file(a, get_file_path(temp_filename)) == JSONSuccess);
    b = json_parse_file(get_file_path(temp_filename));
    remove(temp_filename);
}

void test_suite_9(void) {
    const char *temp_filename = "test_2_serialized_pretty.txt";
    JSON_Value *a = NULL, *b = NULL;
    a = json_parse_file(get_file_path(filename));
    TEST(json_serialize_to_file_pretty(a, get_file_path(temp_filename)) == JSONSuccess);
    b = json_parse_file(get_file_path(temp_filename));
    remove(temp_filename);
}

// Persistence example that creates files in current directory
void persistence_example(void) {
    JSON_Value *user_data = json_parse_file(get_file_path("user_data.json"));
    if (user_data == NULL || json_validate(schema, user_data) != JSONSuccess) {
        user_data = json_value_init_object();
        json_object_set_string(json_object(user_data), "name", buf);
        json_serialize_to_file(user_data, "user_data.json");  // Writes to current dir
    }
}

// External command operations that create/remove files
void print_commits_info(const char *username, const char *repo) {
    const char *output_filename = "commits.json";
    char curl_command[256], cleanup_command[256];
    sprintf(curl_command, "curl -s \"https://api.github.com/repos/%s/%s/commits\" > %s",
            username, repo, output_filename);
    sprintf(cleanup_command, "rm -f %s", output_filename);
    system(curl_command);    // Creates commits.json in current directory
    // ... process file ...
    system(cleanup_command); // Removes commits.json
}
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 551, 556, 559, 567, 574, 577, 809, 773-774, 779, 796
- **Build Requirement**: Test execution requires write access to the working directory for creating temporary files. Standard library functions remove() and system() must be available for file cleanup and external command execution
- **Bazel Mapping Description**: Bazel tests must write files to the directory specified by the $TEST_TMPDIR environment variable, not to the source tree. This requires source code modifications to use $TEST_TMPDIR for file creation instead of writing to the current directory. Standard library functions are automatically available.
- **Bazel Code Snippet**:
```c
// Modified C source code for Bazel $TEST_TMPDIR compatibility:

// Helper function to get temporary directory
static const char* get_temp_dir(void) {
    const char *tmpdir = getenv("TEST_TMPDIR");
    return tmpdir ? tmpdir : ".";
}

// Modified test_suite_8 function
void test_suite_8(void) {
    char temp_path[512];
    const char *tmpdir = get_temp_dir();
    snprintf(temp_path, sizeof(temp_path), "%s/test_2_serialized.txt", tmpdir);
    
    JSON_Value *a = NULL, *b = NULL;
    a = json_parse_file(get_file_path(filename));
    TEST(json_serialize_to_file(a, temp_path) == JSONSuccess);
    b = json_parse_file(temp_path);
    remove(temp_path);
}

// Modified persistence_example function
void persistence_example(void) {
    char user_data_path[512];
    const char *tmpdir = get_temp_dir();
    snprintf(user_data_path, sizeof(user_data_path), "%s/user_data.json", tmpdir);
    
    JSON_Value *user_data = json_parse_file(user_data_path);
    if (user_data == NULL || json_validate(schema, user_data) != JSONSuccess) {
        user_data = json_value_init_object();
        json_object_set_string(json_object(user_data), "name", buf);
        json_serialize_to_file(user_data, user_data_path);
    }
}
```
- **Output Bazel File**: tests.c (source code modifications)
- **References**: [PROPOSED-SAP-FILESYSTEM-002], [TODO]

## Memory Testing Features

### [SA-TESTING-002] Custom Memory Allocation Testing
- **Description**: The test file implements custom memory allocation functions for testing memory leaks and allocation failures, requiring the ability to override standard library functions.

- **Source Code Snippet**:
```c
static int g_malloc_count = 0;
static void *counted_malloc(size_t size);
static void counted_free(void *ptr);

typedef struct failing_alloc {
    int allocation_to_fail;
    int alloc_count;
    int total_count;
    int has_failed;
    int should_fail;
} failing_alloc_t;

static failing_alloc_t g_failing_alloc;
static void *failing_malloc(size_t size);
static void failing_free(void *ptr);
```

- **Path to Source File**: tests.c
- **Line Number in Source File**: 76-94
- **Build Requirement**: Standard library memory allocation functions must be available for override. No special build system configuration required beyond standard library linking
- **Bazel Mapping Description**: Standard library memory allocation functions are automatically available in Bazel C compilation. The custom memory allocation testing functionality is handled entirely by the test source code.
- **Bazel Code Snippet**:
```starlark
cc_test(
    name = "parson_test",
    srcs = ["tests.c"],
    deps = [":parson"],
    # Standard library automatically available
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-TESTING-001], [TODO]
