# Bazel Source Mapping Runbook Proposals: parson.h

This document proposes new bazel source mapping patterns based on the analysis of `parson.h` during the Bazel source mapping process.

## Proposed Bazel Source Mapping Patterns

### [PROPOSED-BSMP-INCLUDE-001] Header Guard Protection
- **Pattern Description**: When C/C++ header files use traditional include guards to prevent multiple inclusions, no special Bazel configuration is needed as this is handled automatically.

- **Source Pattern Identification**: [PROPOSED-SAP-INCLUDE-001]

- **Source Example**:
```c
#ifndef library_library_h
#define library_library_h
// ... content ...
#endif
```

- **Build Requirement**: Standard C/C++ compilation, no special build flags required

- **Bazel Mapping Description**: Header guards are a source code feature that requires no special Bazel configuration. Bazel's cc_library rule handles header compilation correctly with standard include guards.

- **Bazel Code Example**:
```starlark
# No specific Bazel code needed - header guards handled automatically
```

- **Output Bazel File**: N/A

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-INCLUDE-002] C++ Compatibility Extern Blocks
- **Pattern Description**: When C header files provide C++ compatibility through extern "C" blocks, no special Bazel configuration is needed as this is handled automatically when consumed by C++ targets.

- **Source Pattern Identification**: [PROPOSED-SAP-INCLUDE-002]

- **Source Example**:
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

- **Build Requirement**: Enables compilation in both C and C++ contexts, no special build flags required

- **Bazel Mapping Description**: C++ compatibility extern blocks are handled automatically by Bazel when the library is consumed by C++ targets. No special configuration needed in the cc_library rule.

- **Bazel Code Example**:
```starlark
# No specific Bazel code needed - extern "C" handled automatically
```

- **Output Bazel File**: N/A

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-DEPS-003] Standard C Library Header Dependencies
- **Pattern Description**: When C/C++ header files include standard library headers (like stddef.h), these are automatically available in Bazel compilation environments.

- **Source Pattern Identification**: [PROPOSED-SAP-DEPS-003]

- **Source Example**:
```c
#include <stddef.h>   /* size_t */
```

- **Build Requirement**: Standard C library headers must be available in the compilation environment

- **Bazel Mapping Description**: Standard C library headers are automatically available in Bazel C/C++ compilation environment. No explicit dependency declaration required.

- **Bazel Code Example**:
```starlark
# No specific Bazel code needed - standard headers automatically available
```

- **Output Bazel File**: N/A

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-ROOTDIR-001] Public Header Relocation from Root Directory
- **Pattern Description**: When public API headers are located in the root directory, Bazel requires them to be moved to a subdirectory and configured with the includes attribute for proper external access.

- **Source Pattern Identification**: [PROPOSED-SAP-ROOTDIR-001]

- **Source Example**:
```c
/* File location: library.h (in workspace root) */
#ifndef library_library_h
#define library_library_h
/* Public API definitions */
#endif
```

- **Build Requirement**: Build system must handle root directory header access appropriately. Bazel requires special configuration for public headers

- **Bazel Mapping Description**: Bazel does not allow public headers in the root directory. The public header must be moved to a subdirectory (e.g., include/) and the cc_library rule must use the includes attribute to specify the header directory path for proper include resolution by external consumers.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["include/library.h"],  # Public header moved to include/ directory
    includes = ["include"],        # Make include/ directory available to consumers
    visibility = ["//visibility:public"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-API-001] Public API Header Interface
- **Pattern Description**: When header files define the complete public API for a library, they are exposed through the hdrs attribute and made available to consumers through visibility settings.

- **Source Pattern Identification**: [PROPOSED-SAP-API-001]

- **Source Example**:
```c
typedef struct library_object_t Library_Object;
typedef struct library_array_t  Library_Array;

// Function declarations
Library_Object* library_parse_file(const char *filename);
Library_Object* library_parse_string(const char *string);
```

- **Build Requirement**: This header must be made available to any code that wants to use the library, requiring proper include path configuration

- **Bazel Mapping Description**: The public API header is exposed through the hdrs attribute of the cc_library rule and made available to consumers through visibility settings. Client code depends on this library target to access the API.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["library.h"],
    visibility = ["//visibility:public"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-VERSION-002] Embedded Version Constants
- **Pattern Description**: When header files define version constants for compile-time version checking, these are automatically available to any code that includes the header without special Bazel configuration.

- **Source Pattern Identification**: [PROPOSED-SAP-VERSION-002]

- **Source Example**:
```c
#define LIBRARY_VERSION_MAJOR 1
#define LIBRARY_VERSION_MINOR 5
#define LIBRARY_VERSION_PATCH 3
#define LIBRARY_VERSION_STRING "1.5.3"
```

- **Build Requirement**: No special build requirements, these are compile-time constants

- **Bazel Mapping Description**: Version constants are embedded in the header file and require no special Bazel configuration. They are automatically available to any code that includes the header.

- **Bazel Code Example**:
```starlark
# No specific Bazel code needed - version constants embedded in header
```

- **Output Bazel File**: N/A

- **Exceptional Cases**: None.
