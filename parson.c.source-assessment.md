# Source Assessment: parson.c

This document provides a complete analysis of `parson.c` build-relevant characteristics and build system requirements.

## Compiler-Specific Configurations

### [SA-COMPILER-001] MSVC Compiler Security Warning Suppression
- **Description**: The code conditionally defines _CRT_SECURE_NO_WARNINGS for Microsoft Visual C++ compiler to suppress security warnings about deprecated functions.

- **Source Code Snippet**:
```c
#ifdef _MSC_VER
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif /* _CRT_SECURE_NO_WARNINGS */
#endif /* _MSC_VER */
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 25-29
- **Build Requirement**: When building with MSVC, this suppresses warnings. No explicit build system configuration required, but build system should allow compiler-specific defines
- **Bazel Mapping Description**: This compiler-specific define is handled automatically by the C source code itself. No special Bazel configuration is needed as the conditional compilation directive is embedded in the source. Bazel's cc_library rule will compile this code correctly on MSVC platforms.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - handled by source code conditional compilation
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-COMPILER-001], [TODO]

## Header Dependencies

### [SA-DEPS-001] Local Header Dependency
- **Description**: The implementation file includes its corresponding header file, creating a dependency that must be resolved during compilation.

- **Source Code Snippet**:
```c
#include "parson.h"
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 31
- **Build Requirement**: The parson.h header file must be available in the include path during compilation
- **Bazel Mapping Description**: This local header dependency will be handled by including parson.h in the hdrs attribute of the cc_library rule for the parson library. Bazel automatically manages include paths for headers specified in the same target.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    # ... other attributes
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-DEPS-001], [TODO]

### [SA-DEPS-002] Standard C Library Dependencies
- **Description**: The implementation requires multiple standard C library headers for basic functionality including I/O, memory management, string operations, character classification, mathematical functions, and error handling.

- **Source Code Snippet**:
```c
#include <stdarg.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <math.h>
#include <errno.h>
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 43-49
- **Build Requirement**: Standard C library must be available during compilation and linking. May require linking with math library (-lm) on some systems
- **Bazel Mapping Description**: Standard C library headers are automatically available in Bazel C/C++ compilation. The math library dependency is handled by adding "@platforms//os:linux" condition with linkopts for -lm on Linux systems where it's required.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "//conditions:default": [],
    }),
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-DEPS-002], [TODO]

## Version Consistency Validation

### [SA-VERSION-001] Header-Implementation Version Consistency Check
- **Description**: The implementation file validates at compile-time that its version matches the header file version, preventing version mismatches.

- **Source Code Snippet**:
```c
#define PARSON_IMPL_VERSION_MAJOR 1
#define PARSON_IMPL_VERSION_MINOR 5
#define PARSON_IMPL_VERSION_PATCH 3

#if (PARSON_VERSION_MAJOR != PARSON_IMPL_VERSION_MAJOR)\
|| (PARSON_VERSION_MINOR != PARSON_IMPL_VERSION_MINOR)\
|| (PARSON_VERSION_PATCH != PARSON_IMPL_VERSION_PATCH)
#error "parson version mismatch between parson.c and parson.h"
#endif
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 33-40
- **Build Requirement**: Compilation will fail with error if header and implementation versions don't match. Build system must ensure both files are from the same version
- **Bazel Mapping Description**: This version consistency check is enforced by the source code itself during compilation. Bazel ensures both files are built together in the same target, maintaining version consistency by design.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    # Both files are built together, ensuring version consistency
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-VERSION-001], [TODO]

## Build Configuration Options

### [SA-CONFIG-001] Configurable Float Format
- **Description**: The implementation allows customization of floating-point number formatting through a compile-time define, providing build-time configuration flexibility.

- **Source Code Snippet**:
```c
#ifndef PARSON_DEFAULT_FLOAT_FORMAT
#define PARSON_DEFAULT_FLOAT_FORMAT "%1.17g" /* do not increase precision without incresing NUM_BUF_SIZE */
#endif
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 67-69
- **Build Requirement**: Build system can optionally define PARSON_DEFAULT_FLOAT_FORMAT to customize float formatting
- **Bazel Mapping Description**: This optional build-time configuration can be provided via .bazelrc using --copt flags to define custom preprocessor definitions.
- **Bazel Code Snippet**:
```starlark
# Optional: Add to .bazelrc: --copt=-DPARSON_DEFAULT_FLOAT_FORMAT=\"%1.17g\"
```
- **Output Bazel File**: .bazelrc
- **References**: [PROPOSED-SAP-CONFIG-001], [TODO]

### [SA-CONFIG-002] Configurable Number Buffer Size
- **Description**: The implementation allows customization of the buffer size used for number serialization through a compile-time define.

- **Source Code Snippet**:
```c
#ifndef PARSON_NUM_BUF_SIZE
#define PARSON_NUM_BUF_SIZE 64 /* double printed with "%1.17g" shouldn't be longer than 25 bytes so let's be paranoid and use 64 */
#endif
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 71-73
- **Build Requirement**: Build system can optionally define PARSON_NUM_BUF_SIZE to customize buffer size
- **Bazel Mapping Description**: This optional build-time configuration can be provided via .bazelrc using --copt flags to define custom preprocessor definitions for buffer size customization.
- **Bazel Code Snippet**:
```starlark
# Optional: Add to .bazelrc: --copt=-DPARSON_NUM_BUF_SIZE=64
```
- **Output Bazel File**: .bazelrc
- **References**: [PROPOSED-SAP-CONFIG-001], [TODO]

### [SA-CONFIG-003] Configurable Indentation String
- **Description**: The implementation allows customization of the indentation string used for pretty-printing JSON through a compile-time define.

- **Source Code Snippet**:
```c
#ifndef PARSON_INDENT_STR
#define PARSON_INDENT_STR "    "
#endif
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 75-77
- **Build Requirement**: Build system can optionally define PARSON_INDENT_STR to customize indentation format
- **Bazel Mapping Description**: This optional build-time configuration can be provided via .bazelrc using --copt flags to define custom preprocessor definitions for indentation string customization.
- **Bazel Code Snippet**:
```starlark
# Optional: Add to .bazelrc: --copt=-DPARSON_INDENT_STR=\"    \"
```
- **Output Bazel File**: .bazelrc
- **References**: [PROPOSED-SAP-CONFIG-001], [TODO]

## Mathematical Library Dependency

### [SA-MATH-001] Math Library Function Usage
- **Description**: The code uses mathematical functions that may require explicit linking with the math library on some systems.

- **Source Code Snippet**:
```c
#include <math.h>
// Usage in macros and function calls for floating point validation
#if defined(isnan) && defined(isinf)
#define IS_NUMBER_INVALID(x) (isnan((x)) || isinf((x)))
#else
#define IS_NUMBER_INVALID(x) (((x) * 0.0) != 0.0)
#endif
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 48, 82-86
- **Build Requirement**: May require linking with math library (-lm) on Unix-like systems for isnan/isinf functions
- **Bazel Mapping Description**: The math library dependency is handled using Bazel's select() function to conditionally link with -lm on Linux/Unix systems where it's required, while other platforms get the math functions from the standard library.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "//conditions:default": [],
    }),
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-MATH-001], [TODO]

## Memory Management Configuration

### [SA-MALLOC-001] Custom Memory Allocation Functions
- **Description**: The implementation allows runtime configuration of memory allocation functions while using standard malloc/free as defaults.

- **Source Code Snippet**:
```c
#undef malloc
#undef free

static JSON_Malloc_Function parson_malloc = malloc;
static JSON_Free_Function parson_free = free;
```

- **Path to Source File**: parson.c
- **Line Number in Source File**: 89-90, 88, 91
- **Build Requirement**: Standard library malloc/free must be available. Build system should ensure standard library is linked
- **Bazel Mapping Description**: Standard library memory allocation functions are automatically available in Bazel C/C++ compilation. The memory allocation function override mechanism is handled entirely by the source code at runtime.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    # Standard library automatically linked by Bazel
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-MALLOC-001], [TODO]
