# Source Assessment Runbook Proposals: parson.h

This document proposes new source assessment patterns based on the analysis of `parson.h`. These patterns capture C/C++ header file constructs that impose build requirements.

## Header Protection Patterns

### [PROPOSED-SAP-INCLUDE-001] Traditional Header Guard Protection
- **Pattern Description**: Header file that uses traditional include guards to prevent multiple inclusions during compilation, following the standard C/C++ pattern.

- **Pattern Identification**: 
  - `#ifndef UNIQUE_IDENTIFIER` at the beginning of header (after initial comments/license)
  - `#define UNIQUE_IDENTIFIER` immediately following the #ifndef
  - `#endif` at the end of the header file
  - Identifier typically follows pattern: `project_filename_h` or similar

- **Source Example**:
```c
#ifndef library_header_h
#define library_header_h
/* header content */
#endif
```

- **Build Requirement**: Standard C/C++ compilation support - no special build flags required. Essential for proper compilation but handled automatically by preprocessor.

- **Exceptional Cases**: None.

### [PROPOSED-SAP-INCLUDE-002] C++ Compatibility Extern Block
- **Pattern Description**: Header file that provides C++ compatibility through extern "C" blocks, enabling C libraries to be used from C++ code without name mangling.

- **Pattern Identification**: 
  - `#ifdef __cplusplus` conditional at the beginning of header declarations
  - `extern "C" {` block opening after the conditional
  - Matching closing block with `#ifdef __cplusplus` and `}` at the end

- **Source Example**:
```c
#ifdef __cplusplus
extern "C"
{
#endif
/* C declarations */
#ifdef __cplusplus
}
#endif
```

- **Build Requirement**: Enables compilation in both C and C++ contexts without special build flags. Build system should support both C and C++ compilation modes.

- **Exceptional Cases**: None.

## Standard Library Header Dependencies

### [PROPOSED-SAP-DEPS-003] Standard C Type Dependencies
- **Pattern Description**: Header file that includes standard C library headers for essential type definitions required by the public interface.

- **Pattern Identification**: 
  - `#include <stddef.h>` or similar standard headers using angle brackets
  - Typically for fundamental types like size_t, NULL, etc.
  - Located early in header file, before custom type definitions

- **Source Example**:
```c
#include <stddef.h>   /* size_t */
```

- **Build Requirement**: Standard C library headers must be available in the compilation environment. No special linking required for header-only dependencies.

- **Exceptional Cases**: None.

## Root Directory Header Patterns

### [PROPOSED-SAP-ROOTDIR-001] Public Header in Root Directory
- **Pattern Description**: Header file located in the root directory of a workspace that defines a public API intended for consumption by other libraries or projects, which creates specific challenges for Bazel build systems that discourage root directory includes.

- **Pattern Identification**: 
  - Header file (.h, .hpp, .hxx) located in the workspace root directory
  - Contains public API declarations (function prototypes, type definitions, constants)
  - Intended for inclusion by external code or other libraries
  - Not a private/internal header used only within the same compilation unit

- **Source Example**:
```c
/* File: workspace_root/library.h */
#ifndef library_h
#define library_h

#include <stddef.h>

typedef struct library_object_t Library_Object;

Library_Object * library_create(const char *data);
int library_process(Library_Object *obj);
void library_destroy(Library_Object *obj);

#endif
```

- **Build Requirement**: Build system must handle root directory header access appropriately. Bazel specifically discourages root directory includes and may require special configuration or header relocation to follow Bazel idioms for proper dependency management.

- **Exceptional Cases**: None.

## Public API Interface Patterns

### [PROPOSED-SAP-API-001] Public Library Interface Declaration
- **Pattern Description**: Header file that defines the complete public API for a library, exposing function declarations, type definitions, and constants for client use.

- **Pattern Identification**: 
  - Type definitions using typedef for opaque or transparent types
  - Function declarations (prototypes) without implementations
  - Macro definitions for public constants
  - Structured to provide complete interface for library functionality

- **Source Example**:
```c
typedef struct library_object_t Library_Object;
typedef struct library_array_t  Library_Array;

Library_Object * library_create(const char *data);
int library_process(Library_Object *obj);
void library_destroy(Library_Object *obj);

#define LIBRARY_SUCCESS 0
#define LIBRARY_ERROR   1
```

- **Build Requirement**: This header must be available to client code through proper include path configuration. Build system must make header accessible to dependent targets.

- **Exceptional Cases**: None.

## Version Information Patterns

### [PROPOSED-SAP-VERSION-002] Library Version Constants
- **Pattern Description**: Header file that defines version constants for compile-time version checking and library identification.

- **Pattern Identification**: 
  - Macro definitions for version components (MAJOR, MINOR, PATCH)
  - Optional string version definition combining components
  - Consistent naming pattern with library prefix

- **Source Example**:
```c
#define LIBRARY_VERSION_MAJOR 1
#define LIBRARY_VERSION_MINOR 5
#define LIBRARY_VERSION_PATCH 3
#define LIBRARY_VERSION_STRING "1.5.3"
```

- **Build Requirement**: No special build requirements - these are compile-time constants processed by the preprocessor. Can be used for conditional compilation based on version.

- **Exceptional Cases**: None.
