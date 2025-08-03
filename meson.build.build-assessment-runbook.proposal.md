# meson.build Build Assessment Runbook Proposal

This document proposes build assessment patterns for Meson build systems based on analysis of the parson library project.

## Proposal Overview
Based on the Meson build assessment, the following patterns have been identified as fundamental constructs that appear in Meson build systems and require standardized assessment approaches.

## Proposed Build Assessment Patterns

### Project Configuration

#### [PROPOSED-BAP-MESON-PROJECT-001] Project Name Declaration
- **Pattern Description**: Declares the project name which establishes the project identity and sets project variables that can be referenced throughout the build system using functions like meson.project_name().

- **Pattern Identification**: 
  - Meson function `project()`
  - First parameter contains the project name as a string
  - May include additional parameters for language, version, and other settings

- **Source Example**:
```starlark
project('myproject', 'c')
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MESON-PROJECT-002] Project Language Specification
- **Pattern Description**: Specifies the programming language(s) used by the project. This enables language-specific Meson functionality, compiler detection, and language-specific build rules.

- **Pattern Identification**: 
  - Meson function `project()`
  - Language parameter (string) specified after project name
  - Common values include 'c', 'cpp', 'fortran', etc.

- **Source Example**:
```starlark
project('myproject', 'c')
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MESON-PROJECT-003] Project Version Declaration
- **Pattern Description**: Declares the project version which can be used throughout the build system for generating package metadata and referenced using meson.project_version().

- **Pattern Identification**: 
  - Meson function `project()`
  - `version` keyword parameter
  - Version string value

- **Source Example**:
```starlark
project('myproject', 'c', version : '1.0.0')
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MESON-PROJECT-004] Project License Declaration
- **Pattern Description**: Declares the project license which is used for package metadata and distribution purposes.

- **Pattern Identification**: 
  - Meson function `project()`
  - `license` keyword parameter
  - License identifier string (e.g., 'MIT', 'GPL-3.0', 'Apache-2.0')

- **Source Example**:
```starlark
project('myproject', 'c', license : 'MIT')
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MESON-PROJECT-005] Build System Version Requirement
- **Pattern Description**: Specifies the minimum required version of Meson needed to process the build file. This ensures compatibility with Meson features and prevents build failures on older versions.

- **Pattern Identification**: 
  - Meson function `project()`
  - `meson_version` keyword parameter
  - Version requirement string with comparison operator

- **Source Example**:
```starlark
project('myproject', 'c', meson_version : '>=0.50.0')
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MESON-PROJECT-006] Default Build Options
- **Pattern Description**: Sets default build options for the project that control compilation behavior, optimization levels, warning levels, and language standards. These can be overridden by users.

- **Pattern Identification**: 
  - Meson function `project()`
  - `default_options` keyword parameter
  - Array of option strings in 'key=value' format

- **Source Example**:
```starlark
project('myproject', 'c', default_options : ['c_std=c99', 'optimization=2'])
```

- **Exceptional Cases**: None.

### Variable Configuration

#### [PROPOSED-BAP-MESON-VARIABLE-001] Variable Definition
- **Pattern Description**: Defines a variable to store values that can be referenced later in the build system. This provides a way to organize and reuse configuration data.

- **Pattern Identification**: 
  - Variable assignment using `=` operator
  - Variable name on left side
  - Value expression on right side (string, array, function call, etc.)

- **Source Example**:
```starlark
sources = ['file1.c', 'file2.c']
```

- **Exceptional Cases**: None.

### Include Directory Configuration

#### [PROPOSED-BAP-MESON-INCLUDE-001] Include Directory Definition
- **Pattern Description**: Defines include directories that specify where the compiler should look for header files during compilation. Returns an include directory object that can be used in targets.

- **Pattern Identification**: 
  - Meson function `include_directories()`
  - Directory path(s) as parameter(s)
  - Result typically assigned to a variable for later use

- **Source Example**:
```starlark
inc = include_directories('include')
```

- **Exceptional Cases**: None.

### Library Target Configuration

#### [PROPOSED-BAP-MESON-LIBRARY-001] Library Target Creation
- **Pattern Description**: Creates a library target that defines the main build artifact. The library will be built from specified sources and can be configured for installation and linking.

- **Pattern Identification**: 
  - Meson function `library()`
  - Library name as first parameter
  - Optional keyword parameters like `sources`, `install`, `include_directories`
  - Result typically assigned to a variable

- **Source Example**:
```starlark
mylib = library('mylib', sources: src_files, install: true)
```

- **Exceptional Cases**: None.

### Installation Configuration

#### [PROPOSED-BAP-MESON-INSTALL-001] Header Installation
- **Pattern Description**: Configures installation of header files to the standard include directory, making them available for other projects to use when the library is installed.

- **Pattern Identification**: 
  - Meson function `install_headers()`
  - Header file name(s) as parameter(s)
  - Optional `subdir` parameter for installation subdirectory

- **Source Example**:
```starlark
install_headers('mylib.h')
```

- **Exceptional Cases**: None.

### Dependency Configuration

#### [PROPOSED-BAP-MESON-DEPENDENCY-001] Dependency Declaration
- **Pattern Description**: Declares a dependency object that other Meson projects can use to link against this project. This provides a complete interface including libraries and include directories.

- **Pattern Identification**: 
  - Meson function `declare_dependency()`
  - Keyword parameters like `include_directories`, `link_with`, `dependencies`
  - Result typically assigned to a variable with project name

- **Source Example**:
```starlark
mylib_dep = declare_dependency(
    include_directories : inc,
    link_with : mylib
)
```

- **Exceptional Cases**: None.

### Module Configuration

#### [PROPOSED-BAP-MESON-MODULE-001] Module Import
- **Pattern Description**: Imports a Meson module to provide additional functionality beyond the core Meson capabilities. Modules extend the build system with specialized features.

- **Pattern Identification**: 
  - Meson function `import()`
  - Module name as string parameter
  - Result assigned to variable for accessing module functions

- **Source Example**:
```starlark
pkgconfig = import('pkgconfig')
```

- **Exceptional Cases**: None.

### Pkgconfig Configuration

#### [PROPOSED-BAP-MESON-PKGCONFIG-001] Pkg-config File Generation
- **Pattern Description**: Generates a pkg-config file that allows other build systems and tools to discover and use the library. This provides metadata about the library including version, description, and linking information.

- **Pattern Identification**: 
  - Method call on pkgconfig module object
  - `generate()` method name
  - Library target as first parameter
  - Optional keyword parameters like `version`, `name`, `description`, `filebase`

- **Source Example**:
```starlark
pkgconfig.generate(mylib,
    version: meson.project_version(),
    name: 'mylib',
    description: 'My library description'
)
```

- **Exceptional Cases**: None.
