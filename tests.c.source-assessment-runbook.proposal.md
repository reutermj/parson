# Source Assessment Runbook Proposals: tests.c

This document proposes new source assessment patterns based on the analysis of `tests.c`. These patterns capture C/C++ test file constructs that impose build requirements.

## Compiler-Specific Test Configurations

### [PROPOSED-SAP-COMPILER-002] Apple Clang Deprecation Warning Suppression
- **Pattern Description**: Test code that conditionally disables deprecation warnings when building with Apple Clang compiler to avoid warnings about deprecated API usage during testing.

- **Pattern Identification**: 
  - Combined conditional check for `#if defined(__APPLE__) && defined(__clang__)`
  - Use of `#pragma clang diagnostic ignored` with specific warning flag
  - Typically targets "-Wdeprecated-declarations" or similar warnings

- **Source Example**:
```c
#if defined(__APPLE__) && defined(__clang__)
    #pragma clang diagnostic ignored "-Wdeprecated-declarations"
#endif
```

- **Build Requirement**: Apple Clang compiler support with pragma directive capability. No special build flags needed but compiler must support clang-specific pragma syntax.

- **Exceptional Cases**: None.

## Test Executable Configuration Patterns

### [PROPOSED-SAP-EXECUTABLE-001] Conditional Main Function for Tests
- **Pattern Description**: Test file that includes a main function conditionally compiled based on a preprocessor define, allowing tests to be built as either standalone executable or as part of a larger test suite.

- **Pattern Identification**: 
  - `#ifdef TESTS_MAIN` or similar conditional compilation directive
  - `int main(int argc, char *argv[])` function definition within the conditional block
  - Test execution logic within the main function

- **Source Example**:
```c
#ifdef TESTS_MAIN
int main(int argc, char *argv[]) {
    /* test execution code */
    return test_result;
}
#endif
```

- **Build Requirement**: To build as standalone executable, define the conditional macro (e.g., TESTS_MAIN) during compilation. Build system must support conditional compilation and executable target creation.

- **Exceptional Cases**: None.

## Test Framework Patterns

### [PROPOSED-SAP-TEST-001] Custom Test Macro Framework
- **Pattern Description**: Test file that defines custom testing macros for assertion and comparison operations, implementing a lightweight test framework within the source.

- **Pattern Identification**: 
  - Macro definitions using `#define` for test operations (e.g., TEST, ASSERT, EXPECT)
  - Macros typically use do-while(0) pattern for statement-like behavior
  - Often includes counter variables for tracking test results
  - May include specialized comparison macros (string equality, floating point equality)

- **Source Example**:
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
#define DBL_EQ(a, b) (fabs((a) - (b)) < EPSILON)
```

- **Build Requirement**: Standard C library functions must be available (printf, strcmp, fabs, etc.). May require math library linking when using mathematical comparison functions.

- **Exceptional Cases**: 
  - **Exception Description**: When mathematical comparison macros are used, additional math library linking may be required
  - **Exception Pattern Identification**:
    - Presence of mathematical functions like fabs() in macro definitions
    - Target system is Unix-like requiring explicit math library linking
  - **Exception Source Example**:
```c
#define DBL_EQ(a, b) (fabs((a) - (b)) < DBL_EPSILON)
```
  - **Exception Build Requirement**: Standard C library plus math library linking (-lm)
  - **Exception Handler Reference**: [PROPOSED-SAP-MATH-001]

## File System Access Patterns

### [PROPOSED-SAP-FILESYSTEM-001] Test Data File Access
- **Pattern Description**: Test code that references external test data files and requires file system read access for loading test inputs and expected outputs.

- **Pattern Identification**: 
  - String constants or variables defining test data directory paths
  - Functions for reading files (custom or standard library)
  - File path construction functions combining directory and filename
  - References to specific test data files

- **Source Example**:
```c
static const char *g_tests_path = "testdata";

static char * read_file(const char * filename);
const char* get_file_path(const char *filename);

/* Usage */
data = read_file(get_file_path("sample.json"));
```

- **Build Requirement**: Test execution requires read access to test data files. Build system must ensure test files are available at runtime, typically by copying test data to build output directory or running tests from appropriate working directory.

- **Exceptional Cases**: None.

### [PROPOSED-SAP-FILESYSTEM-002] Test File Write Operations
- **Pattern Description**: Test code that creates, modifies, and cleans up temporary files during test execution for testing serialization, file I/O, and persistence functionality.

- **Pattern Identification**: 
  - File creation/writing operations using standard library functions
  - Temporary file name generation or hardcoded temporary files
  - File cleanup operations using remove() or similar functions
  - May include external command execution using system() calls

- **Source Example**:
```c
/* Temporary file operations */
const char *temp_filename = "temp_test.dat";
write_data_to_file(data, temp_filename);
/* test file contents */
remove(temp_filename);

/* External command execution */
system("curl -o data.json http://api.example.com/data");
/* process data.json */
system("rm -f data.json");
```

- **Build Requirement**: Test execution requires write access to working directory for temporary files. Standard library functions (remove, system) must be available. Build system should ensure proper cleanup of test artifacts.

- **Exceptional Cases**: 
  - **Exception Description**: When system() calls are used, additional security and platform considerations apply
  - **Exception Pattern Identification**:
    - Presence of system() function calls
    - External command execution within test code
  - **Exception Source Example**:
```c
system("external_command arg1 arg2");
```
  - **Exception Build Requirement**: System command execution capability required, with potential security implications for build environment
  - **Exception Handler Reference**: NONE

## Memory Testing Patterns

### [PROPOSED-SAP-TESTING-001] Custom Memory Allocation Override for Testing
- **Pattern Description**: Test file that implements custom memory allocation functions for testing memory management, leak detection, and allocation failure scenarios.

- **Pattern Identification**: 
  - Custom malloc/free function implementations
  - Global counters or state variables for tracking allocations
  - Function pointers or direct replacement of allocation functions
  - Often includes failure injection mechanisms

- **Source Example**:
```c
static int g_malloc_count = 0;
static void *counted_malloc(size_t size);
static void counted_free(void *ptr);

typedef struct failing_alloc {
    int allocation_to_fail;
    int alloc_count;
    int has_failed;
} failing_alloc_t;

static failing_alloc_t g_failing_alloc;
static void *failing_malloc(size_t size);
static void failing_free(void *ptr);
```

- **Build Requirement**: Standard library memory allocation functions must be available for override and delegation. No special build system configuration required beyond standard library linking.

- **Exceptional Cases**: None.
