# Bazel Source Mapping Runbook Proposals: parson.c

This document proposes new bazel source mapping patterns based on the analysis of `parson.c` during the Bazel source mapping process.

## Proposed Bazel Source Mapping Patterns

### [PROPOSED-BSMP-COMPILER-001] Compiler-Specific Conditional Compilation
- **Pattern Description**: When C/C++ source code uses conditional compilation to handle compiler-specific configurations (like MSVC warning suppression), no special Bazel configuration is needed as the compiler detection is embedded in the source code.

- **Source Pattern Identification**: [PROPOSED-SAP-COMPILER-001]

- **Source Example**:
```c
#ifdef _MSC_VER
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif
#endif
```

- **Build Requirement**: Compiler-specific defines need to be handled appropriately without breaking builds on other compilers

- **Bazel Mapping Description**: Bazel's cc_library rule automatically handles compiler-specific conditional compilation directives that are embedded in the source code. No special configuration is needed in the BUILD file.

- **Bazel Code Example**:
```starlark
# No specific Bazel code needed - handled by source code conditional compilation
```

- **Output Bazel File**: N/A

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-DEPS-001] Local Header Dependencies
- **Pattern Description**: When C/C++ implementation files include their corresponding header files, this creates a local dependency that must be resolved during compilation by including the header in the same target.

- **Source Pattern Identification**: [PROPOSED-SAP-DEPS-001]

- **Source Example**:
```c
#include "library.h"
```

- **Build Requirement**: The corresponding header file must be available in the include path during compilation

- **Bazel Mapping Description**: Local header dependencies are handled by including the header file in the hdrs attribute of the cc_library rule for the same target. Bazel automatically manages include paths for headers specified in the same target.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["library.h"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-DEPS-002] Standard C Library Dependencies with Math Library
- **Pattern Description**: When C/C++ code includes standard library headers including math.h, it may require linking with the math library (-lm) on some Unix-like systems.

- **Source Pattern Identification**: [PROPOSED-SAP-DEPS-002]

- **Source Example**:
```c
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
```

- **Build Requirement**: Standard C library must be available during compilation and linking. May require linking with math library (-lm) on some systems

- **Bazel Mapping Description**: Standard C library headers are automatically available in Bazel C/C++ compilation. The math library dependency is handled using Bazel's select() function to conditionally link with -lm on platforms where it's required.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["library.h"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "//conditions:default": [],
    }),
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-VERSION-001] Compile-Time Version Consistency Checks
- **Pattern Description**: When C/C++ implementation files validate at compile-time that their version matches the header file version using #error directives, Bazel ensures version consistency by building both files together in the same target.

- **Source Pattern Identification**: [PROPOSED-SAP-VERSION-001]

- **Source Example**:
```c
#define IMPL_VERSION_MAJOR 1
#define IMPL_VERSION_MINOR 5

#if (LIB_VERSION_MAJOR != IMPL_VERSION_MAJOR) || (LIB_VERSION_MINOR != IMPL_VERSION_MINOR)
#error "version mismatch between header and implementation"
#endif
```

- **Build Requirement**: Compilation will fail with error if header and implementation versions don't match. Build system must ensure both files are from the same version

- **Bazel Mapping Description**: Version consistency checks are enforced by the source code itself during compilation. Bazel ensures both files are built together in the same target, maintaining version consistency by design.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["library.h"],
    # Both files are built together, ensuring version consistency
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-CONFIG-001] Optional Build-Time Configuration via .bazelrc
- **Pattern Description**: When C/C++ code provides optional compile-time configuration through #ifndef preprocessor defines, these can be configured via .bazelrc using --copt flags rather than hardcoding them in BUILD files.

- **Source Pattern Identification**: [PROPOSED-SAP-CONFIG-001]

- **Source Example**:
```c
#ifndef LIBRARY_DEFAULT_FORMAT
#define LIBRARY_DEFAULT_FORMAT "%1.17g"
#endif

#ifndef LIBRARY_BUFFER_SIZE
#define LIBRARY_BUFFER_SIZE 64
#endif
```

- **Build Requirement**: Build system can optionally define preprocessor symbols to customize library behavior

- **Bazel Mapping Description**: Optional build-time configurations can be provided via .bazelrc using --copt flags to define custom preprocessor definitions, keeping the cc_library rule clean of optional configurations.

- **Bazel Code Example**:
```starlark
# Optional: Add to .bazelrc: --copt=-DLIBRARY_DEFAULT_FORMAT=\"%1.17g\"
# Optional: Add to .bazelrc: --copt=-DLIBRARY_BUFFER_SIZE=64
```

- **Output Bazel File**: .bazelrc

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-MATH-001] Math Library Function Usage
- **Pattern Description**: When C/C++ code uses mathematical functions from math.h that may require explicit linking with the math library on some systems.

- **Source Pattern Identification**: [PROPOSED-SAP-MATH-001]

- **Source Example**:
```c
#include <math.h>

#if defined(isnan) && defined(isinf)
#define IS_NUMBER_INVALID(x) (isnan((x)) || isinf((x)))
#else
#define IS_NUMBER_INVALID(x) (((x) * 0.0) != 0.0)
#endif
```

- **Build Requirement**: May require linking with math library (-lm) on Unix-like systems for math functions

- **Bazel Mapping Description**: Math library dependency is handled using Bazel's select() function to conditionally link with -lm on Linux/Unix systems where it's required, while other platforms get the math functions from the standard library.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["library.h"],
    linkopts = select({
        "@platforms//os:linux": ["-lm"],
        "@platforms//os:freebsd": ["-lm"],
        "//conditions:default": [],
    }),
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BSMP-MALLOC-001] Standard Library Memory Allocation
- **Pattern Description**: When C/C++ code uses standard library memory allocation functions (malloc/free) with optional runtime override mechanisms, these are automatically available in Bazel compilation.

- **Source Pattern Identification**: [PROPOSED-SAP-MALLOC-001]

- **Source Example**:
```c
#undef malloc
#undef free

static void* (*library_malloc)(size_t) = malloc;
static void (*library_free)(void*) = free;
```

- **Build Requirement**: Standard library malloc/free must be available. Build system should ensure standard library is linked

- **Bazel Mapping Description**: Standard library memory allocation functions are automatically available in Bazel C/C++ compilation. The memory allocation function override mechanism is handled entirely by the source code at runtime.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "library",
    srcs = ["library.c"],
    hdrs = ["library.h"],
    # Standard library automatically linked by Bazel
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.
