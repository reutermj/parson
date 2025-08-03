# Source Assessment Runbook Proposals: parson.c

This document proposes new source assessment patterns based on the analysis of `parson.c`. These patterns capture C/C++ source code constructs that impose build requirements.

## Compiler-Specific Configuration Patterns

### [PROPOSED-SAP-COMPILER-001] MSVC Security Warning Suppression
- **Pattern Description**: Source code that conditionally defines _CRT_SECURE_NO_WARNINGS for Microsoft Visual C++ compiler to suppress security warnings about deprecated functions like strcpy, sprintf, etc.

- **Pattern Identification**: 
  - Presence of `#ifdef _MSC_VER` preprocessor conditional
  - Definition of `_CRT_SECURE_NO_WARNINGS` macro within the conditional block
  - May include additional conditional check for `#ifndef _CRT_SECURE_NO_WARNINGS`

- **Source Example**:
```c
#ifdef _MSC_VER
#ifndef _CRT_SECURE_NO_WARNINGS
#define _CRT_SECURE_NO_WARNINGS
#endif /* _CRT_SECURE_NO_WARNINGS */
#endif /* _MSC_VER */
```

- **Build Requirement**: No explicit build system configuration required. The build system should allow compiler-specific defines to be processed normally. When building with MSVC, this will suppress security warnings automatically.

- **Exceptional Cases**: None.

## Header Dependency Patterns

### [PROPOSED-SAP-DEPS-001] Local Header Inclusion
- **Pattern Description**: Source code that includes a header file from the same project using quotes syntax, creating a compilation dependency that must be resolved.

- **Pattern Identification**: 
  - Presence of `#include "filename.h"` directive using quotes (not angle brackets)
  - Header filename typically matches or relates to the source filename

- **Source Example**:
```c
#include "library.h"
```

- **Build Requirement**: The referenced header file must be available in the include path during compilation. Build system must ensure header is accessible and handle dependency ordering.

- **Exceptional Cases**: None.

### [PROPOSED-SAP-DEPS-002] Standard C Library Multiple Headers
- **Pattern Description**: Source code that includes multiple standard C library headers, indicating comprehensive dependency on standard library functionality.

- **Pattern Identification**: 
  - Multiple `#include <header.h>` directives for standard C library headers
  - Common headers include: stdio.h, stdlib.h, string.h, stdarg.h, ctype.h, math.h, errno.h

- **Source Example**:
```c
#include <stdarg.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <ctype.h>
#include <math.h>
#include <errno.h>
```

- **Build Requirement**: Standard C library must be available during compilation and linking. May require explicit linking with math library (-lm) on some Unix-like systems when math.h is included.

- **Exceptional Cases**: None.

## Version Consistency Patterns

### [PROPOSED-SAP-VERSION-001] Header-Implementation Version Validation
- **Pattern Description**: Source code that validates at compile-time that implementation version constants match corresponding header version constants, preventing version mismatches.

- **Pattern Identification**: 
  - Definition of implementation-specific version constants (typically with "IMPL" in the name)
  - Compile-time comparison using #if directive between implementation and header version constants
  - Use of #error directive to fail compilation when versions don't match

- **Source Example**:
```c
#define LIBRARY_IMPL_VERSION_MAJOR 1
#define LIBRARY_IMPL_VERSION_MINOR 5
#define LIBRARY_IMPL_VERSION_PATCH 3

#if (LIBRARY_VERSION_MAJOR != LIBRARY_IMPL_VERSION_MAJOR)\
|| (LIBRARY_VERSION_MINOR != LIBRARY_IMPL_VERSION_MINOR)\
|| (LIBRARY_VERSION_PATCH != LIBRARY_IMPL_VERSION_PATCH)
#error "version mismatch between implementation and header"
#endif
```

- **Build Requirement**: Compilation will fail with error if header and implementation versions don't match. Build system must ensure both files are from the same version and handle version synchronization.

- **Exceptional Cases**: None.

## Build Configuration Patterns

### [PROPOSED-SAP-CONFIG-001] Configurable Compile-Time Constants
- **Pattern Description**: Source code that allows customization of behavior through optional compile-time defines with fallback defaults.

- **Pattern Identification**: 
  - Use of `#ifndef MACRO_NAME` followed by `#define MACRO_NAME default_value`
  - Macro name typically indicates its purpose (e.g., format strings, buffer sizes, configuration strings)

- **Source Example**:
```c
#ifndef LIBRARY_DEFAULT_FORMAT
#define LIBRARY_DEFAULT_FORMAT "%1.17g"
#endif

#ifndef LIBRARY_BUFFER_SIZE
#define LIBRARY_BUFFER_SIZE 64
#endif

#ifndef LIBRARY_INDENT_STRING
#define LIBRARY_INDENT_STRING "    "
#endif
```

- **Build Requirement**: Build system can optionally define these macros to customize behavior. No special configuration required - standard C preprocessor handling is sufficient.

- **Exceptional Cases**: None.

## Mathematical Library Patterns

### [PROPOSED-SAP-MATH-001] Math Library Function Usage
- **Pattern Description**: Source code that uses mathematical functions which may require explicit linking with the math library on some systems.

- **Pattern Identification**: 
  - Inclusion of `#include <math.h>`
  - Usage of math functions like isnan(), isinf(), or mathematical operations in macros
  - Often combined with conditional compilation for different math function availability

- **Source Example**:
```c
#include <math.h>

#if defined(isnan) && defined(isinf)
#define IS_NUMBER_INVALID(x) (isnan((x)) || isinf((x)))
#else
#define IS_NUMBER_INVALID(x) (((x) * 0.0) != 0.0)
#endif
```

- **Build Requirement**: May require linking with math library (-lm) on Unix-like systems. Build system should conditionally add math library linking based on target platform.

- **Exceptional Cases**: 
  - **Exception Description**: On Windows with MSVC, math functions are part of the C runtime and don't require separate linking
  - **Exception Pattern Identification**:
    - Target platform is Windows
    - Compiler is MSVC
  - **Exception Source Example**:
```c
#include <math.h>
// Same usage but on Windows/MSVC
#define IS_NUMBER_INVALID(x) (isnan((x)) || isinf((x)))
```
  - **Exception Build Requirement**: No additional library linking required - math functions available in standard C runtime
  - **Exception Handler Reference**: NONE

## Memory Management Patterns

### [PROPOSED-SAP-MALLOC-001] Custom Memory Allocation Override
- **Pattern Description**: Source code that provides runtime-configurable memory allocation functions while using standard library functions as defaults.

- **Pattern Identification**: 
  - Use of `#undef malloc` and `#undef free` to remove standard macro definitions
  - Declaration of function pointers for custom allocation functions
  - Initialization of function pointers with standard malloc/free functions

- **Source Example**:
```c
#undef malloc
#undef free

static MemAlloc_Function custom_malloc = malloc;
static MemFree_Function custom_free = free;
```

- **Build Requirement**: Standard library malloc/free must be available for linking. Build system should ensure standard library is properly linked.

- **Exceptional Cases**: None.
