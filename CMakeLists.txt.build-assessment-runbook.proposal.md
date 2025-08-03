# CMakeLists.txt Build Assessment Runbook Proposal

This document proposes build assessment patterns for CMake build systems based on analysis of the parson library project.

## Proposal Overview
Based on the CMake build assessment, the following patterns have been identified as fundamental constructs that appear in CMake build systems and require standardized assessment approaches.

## Proposed Build Assessment Patterns

### Version Configuration

#### [PROPOSED-BAP-CMAKE-VERSION-001] Build System Version Requirement
- **Pattern Description**: Specifies the minimum required version of the build system needed to process the build file. This ensures compatibility with build system features and prevents build failures on older versions.

- **Pattern Identification**: 
  - CMake command `cmake_minimum_required`
  - VERSION parameter with version number specification
  - Appears at the beginning of CMakeLists.txt files

- **Source Example**:
```cmake
cmake_minimum_required(VERSION 3.5)
```

- **Exceptional Cases**: None.

### Project Configuration

#### [PROPOSED-BAP-CMAKE-PROJECT-001] Project Name Declaration
- **Pattern Description**: Declares the project name which establishes the project identity and sets project variables that can be referenced throughout the build system.

- **Pattern Identification**: 
  - CMake command `project`
  - First parameter contains the project name
  - May include additional parameters for language and other settings

- **Source Example**:
```cmake
project(myproject)
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-CMAKE-PROJECT-002] Project Language Specification
- **Pattern Description**: Specifies the programming language(s) used by the project. This enables language-specific build system functionality, compiler detection, and language-specific build rules.

- **Pattern Identification**: 
  - CMake command `project`
  - Language parameter (C, CXX, Fortran, etc.) specified after project name
  - Enables language-specific CMake variables and functionality

- **Source Example**:
```cmake
project(myproject C)
```

- **Exceptional Cases**: None.

### Module Configuration

#### [PROPOSED-BAP-CMAKE-MODULE-001] Module Import
- **Pattern Description**: Imports a CMake module to provide additional functionality, commands, or variables. This extends the build system capabilities beyond the core CMake functionality.

- **Pattern Identification**: 
  - CMake command `include`
  - Module name parameter (with or without parentheses)
  - May reference built-in modules or custom module files

- **Source Example**:
```cmake
include(ModuleName)
```

- **Exceptional Cases**: None.

### Variable Configuration

#### [PROPOSED-BAP-CMAKE-VARIABLE-001] Variable Definition
- **Pattern Description**: Defines a CMake variable to store a value that can be referenced later in the build system. This provides a way to centralize configuration and avoid repetition.

- **Pattern Identification**: 
  - CMake command `set`
  - Variable name as first parameter
  - Value as subsequent parameter(s)

- **Source Example**:
```cmake
set(VARIABLE_NAME value)
```

- **Exceptional Cases**: None.

### Library Target Configuration

#### [PROPOSED-BAP-CMAKE-LIBRARY-001] Library Target Creation
- **Pattern Description**: Creates a library target that defines a build target which will produce a library artifact and can be referenced by other build system commands and targets.

- **Pattern Identification**: 
  - CMake command `add_library`
  - Target name as first parameter
  - May include library type (STATIC, SHARED, MODULE) or source files

- **Source Example**:
```cmake
add_library(mylib)
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-CMAKE-LIBRARY-002] Library Source File Assignment
- **Pattern Description**: Assigns source files to a library target, specifying which source files will be compiled to create the library artifact.

- **Pattern Identification**: 
  - CMake command `add_library`
  - Target name as first parameter
  - Source file names as subsequent parameters

- **Source Example**:
```cmake
add_library(mylib source1.c source2.c)
```

- **Exceptional Cases**: None.

### Include Directory Configuration

#### [PROPOSED-BAP-CMAKE-INCLUDE-001] Target Include Directory Configuration
- **Pattern Description**: Configures include directories for a target, specifying where the compiler should look for header files during compilation and how include paths are propagated to dependent targets.

- **Pattern Identification**: 
  - CMake command `target_include_directories`
  - Target name as first parameter
  - Scope specifier (PUBLIC, PRIVATE, INTERFACE)
  - Directory paths or generator expressions

- **Source Example**:
```cmake
target_include_directories(mylib PUBLIC include)
```

- **Exceptional Cases**: None.

### Target Properties Configuration

#### [PROPOSED-BAP-CMAKE-PROPERTIES-001] Target Property Setting
- **Pattern Description**: Sets properties on a target to configure its behavior, metadata, or build characteristics. Properties control various aspects of how the target is built and used.

- **Pattern Identification**: 
  - CMake command `set_target_properties`
  - Target name(s) as parameter
  - PROPERTIES keyword
  - Property name and value pairs

- **Source Example**:
```cmake
set_target_properties(target_name PROPERTIES PROPERTY_NAME "property_value")
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-CMAKE-PROPERTIES-002] Public Header Property Setting
- **Pattern Description**: Sets the PUBLIC_HEADER property on a target to specify which header files should be installed as part of the public interface when the target is installed.

- **Pattern Identification**: 
  - CMake command `set_target_properties`
  - Target name as parameter
  - PROPERTIES keyword
  - PUBLIC_HEADER property name
  - Header file name(s) as value

- **Source Example**:
```cmake
set_target_properties(target_name PROPERTIES PUBLIC_HEADER "header.h")
```

- **Exceptional Cases**: None.

### Installation Configuration

#### [PROPOSED-BAP-CMAKE-INSTALL-001] Target Installation Configuration
- **Pattern Description**: Configures how build targets are installed to the system, specifying installation destinations for different types of artifacts and enabling export for use by other projects.

- **Pattern Identification**: 
  - CMake command `install`
  - TARGETS keyword
  - Target name(s)
  - Destination specifications (RUNTIME, LIBRARY, ARCHIVE, PUBLIC_HEADER)
  - Optional EXPORT clause

- **Source Example**:
```cmake
install(TARGETS mylib
    LIBRARY DESTINATION lib
    PUBLIC_HEADER DESTINATION include)
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-CMAKE-INSTALL-002] Export Installation Configuration
- **Pattern Description**: Installs export configuration files that allow other CMake projects to find and use the installed targets through find_package(). This enables proper CMake package integration.

- **Pattern Identification**: 
  - CMake command `install`
  - EXPORT keyword
  - Export name matching previous EXPORT specification
  - FILE parameter with config filename
  - DESTINATION for config file location

- **Source Example**:
```cmake
install(EXPORT mytargets
    FILE MyConfig.cmake
    DESTINATION lib/cmake/myproject)
```

- **Exceptional Cases**: None.
