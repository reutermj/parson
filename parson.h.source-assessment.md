# Source Assessment: parson.h

This document provides a complete analysis of `parson.h` build-relevant characteristics and build system requirements.

## Header Guard and C++ Compatibility

### [SA-INCLUDE-001] Header Guard Protection
- **Description**: The header file uses traditional include guards to prevent multiple inclusions during compilation, which requires no special build system configuration but is essential for proper compilation in C/C++ projects.

- **Source Code Snippet**:
```c
#ifndef parson_parson_h
#define parson_parson_h
// ... content ...
#endif
```

- **Path to Source File**: parson.h
- **Line Number in Source File**: 26, 274
- **Build Requirement**: Standard C/C++ compilation, no special build flags required
- **Bazel Mapping Description**: Header guards are a source code feature that requires no special Bazel configuration. Bazel's cc_library rule handles header compilation correctly with standard include guards.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - header guards handled automatically
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-INCLUDE-001], [TODO]

### [SA-INCLUDE-002] C++ Compatibility Extern Block
- **Description**: The header provides C++ compatibility through extern "C" blocks, enabling the C library to be used from C++ code without name mangling issues.

- **Source Code Snippet**:
```c
#ifdef __cplusplus
extern "C"
{
#endif
// ... content ...
#ifdef __cplusplus
}
#endif
```

- **Path to Source File**: parson.h
- **Line Number in Source File**: 29-32, 270-272
- **Build Requirement**: Enables compilation in both C and C++ contexts, no special build flags required
- **Bazel Mapping Description**: C++ compatibility extern blocks are handled automatically by Bazel when the library is consumed by C++ targets. No special configuration needed in the cc_library rule.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - extern "C" handled automatically
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-INCLUDE-002], [TODO]

## Standard Library Dependencies

### [SA-DEPS-001] Standard C Library Dependency
- **Description**: The header includes stddef.h for size_t type definition, requiring standard C library headers to be available during compilation.

- **Source Code Snippet**:
```c
#include <stddef.h>   /* size_t */
```

- **Path to Source File**: parson.h
- **Line Number in Source File**: 41
- **Build Requirement**: Standard C library headers must be available in the compilation environment
- **Bazel Mapping Description**: Standard C library headers like stddef.h are automatically available in Bazel C/C++ compilation environment. No explicit dependency declaration required.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - standard headers automatically available
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-DEPS-003], [TODO]

## Root Directory Header Location

### [SA-ROOTDIR-001] Public Header in Workspace Root
- **Description**: The parson.h header file is located in the root directory of the workspace and serves as the main public API header, which may require special handling in build systems like Bazel that discourage root directory includes.

- **Source Code Snippet**:
```c
/* File location: parson.h (in workspace root) */
#ifndef parson_parson_h
#define parson_parson_h
/* Public API definitions */
#endif
```

- **Path to Source File**: parson.h
- **Line Number in Source File**: 1 (entire file location)
- **Build Requirement**: Build system must handle root directory header access appropriately. Bazel may require special configuration or header relocation to follow Bazel idioms for proper dependency management
- **Bazel Mapping Description**: Bazel does not allow public headers in the root directory. The public header must be moved to a subdirectory (e.g., include/, public-headers/) and the cc_library rule must use the includes attribute to specify the header directory path for proper include resolution by external consumers.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["include/parson.h"],  # Public header moved to include/ directory
    includes = ["include"],        # Make include/ directory available to consumers
    visibility = ["//visibility:public"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-ROOTDIR-001], [TODO]

## Public API Interface

### [SA-API-001] Public Header Interface
- **Description**: This header file defines the complete public API for the parson JSON library, exposing function declarations, type definitions, and constants that client code can use.

- **Source Code Snippet**:
```c
typedef struct json_object_t JSON_Object;
typedef struct json_array_t  JSON_Array;
typedef struct json_value_t  JSON_Value;

// Function declarations
JSON_Value * json_parse_file(const char *filename);
JSON_Value * json_parse_string(const char *string);
```

- **Path to Source File**: parson.h
- **Line Number in Source File**: 44-46, 89-94
- **Build Requirement**: This header must be made available to any code that wants to use the parson library, requiring proper include path configuration
- **Bazel Mapping Description**: The public API header is exposed through the hdrs attribute of the cc_library rule and made available to consumers through visibility settings. Client code depends on this library target to access the API.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    visibility = ["//visibility:public"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-SAP-API-001], [TODO]

## Version Information

### [SA-VERSION-001] Library Version Definitions
- **Description**: The header defines version constants that can be used for compile-time version checking and library identification.

- **Source Code Snippet**:
```c
#define PARSON_VERSION_MAJOR 1
#define PARSON_VERSION_MINOR 5
#define PARSON_VERSION_PATCH 3
#define PARSON_VERSION_STRING "1.5.3"
```

- **Path to Source File**: parson.h
- **Line Number in Source File**: 35-39
- **Build Requirement**: No special build requirements, these are compile-time constants
- **Bazel Mapping Description**: Version constants are embedded in the header file and require no special Bazel configuration. They are automatically available to any code that includes the header.
- **Bazel Code Snippet**:
```starlark
# No specific Bazel code needed - version constants embedded in header
```
- **Output Bazel File**: N/A
- **References**: [PROPOSED-SAP-VERSION-002], [TODO]
