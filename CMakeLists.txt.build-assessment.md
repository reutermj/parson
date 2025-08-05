# CMakeLists.txt Build Assessment

This document provides a comprehensive analysis of the CMake build system for the parson library project.

## Project Overview
The CMake build system creates a C library for parson, a lightweight JSON library, with proper installation and packaging support.

## Version Configuration

### [BA-CMAKE-VERSION-001] CMake Minimum Version Requirement
- **Description**: Specifies the minimum required version of CMake needed to process this CMakeLists.txt file. This ensures compatibility with CMake features and prevents build failures on older CMake versions.

- **Source Code Snippet**:
```cmake
cmake_minimum_required(VERSION 3.5)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 1
- **Bazel Mapping Description**: Bazel doesn't have explicit version requirements in build files. Version compatibility is managed by the Bazel binary version itself. No direct translation needed.
- **Bazel Code Snippet**:
```starlark
# No direct Bazel equivalent - version compatibility handled by Bazel binary
```
- **Output Bazel File**: N/A (no direct translation)
- **References**: [PROPOSED-BAP-CMAKE-VERSION-001] Build System Version Requirement, [TODO] Bazel Version Management

## Project Configuration

### [BA-CMAKE-PROJECT-001] Project Name Declaration
- **Description**: Declares the CMake project name as "parson". This establishes the project identity and sets the PROJECT_NAME variable that can be referenced throughout the build system.

- **Source Code Snippet**:
```cmake
project(parson C)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 2
- **Bazel Mapping Description**: Bazel project identity is established through MODULE.bazel or WORKSPACE file. Project name is typically the module name or repository name.
- **Bazel Code Snippet**:
```starlark
module(
    name = "parson",
    version = "1.5.3",
)
```
- **Output Bazel File**: MODULE.bazel
- **References**: [PROPOSED-BAP-CMAKE-PROJECT-001] Project Name Declaration, [TODO] Bazel Project Identity

### [BA-CMAKE-PROJECT-002] Project Language Specification
- **Description**: Specifies that this CMake project uses the C programming language. This enables C-specific CMake functionality, compiler detection, and language-specific build rules.

- **Source Code Snippet**:
```cmake
project(parson C)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 2
- **Bazel Mapping Description**: Bazel language specification is handled through bazel_dep on rules_cc in MODULE.bazel. C language support is provided by the rules_cc ruleset.
- **Bazel Code Snippet**:
```starlark
bazel_dep(name = "rules_cc", version = "0.1.4")
```
- **Output Bazel File**: MODULE.bazel
- **References**: [PROPOSED-BAP-CMAKE-PROJECT-002] Project Language Specification, [TODO] Bazel Language Detection

## Module Configuration

### [BA-CMAKE-MODULE-001] GNU Install Directories Module Import
- **Description**: Imports the GNUInstallDirs module to provide standard installation directory variables (CMAKE_INSTALL_BINDIR, CMAKE_INSTALL_LIBDIR, etc.) following GNU conventions.

- **Source Code Snippet**:
```cmake
include (GNUInstallDirs)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 4
- **Bazel Mapping Description**: Bazel doesn't have equivalent module imports for installation directories. Installation paths are handled through packaging rules and command-line flags. No direct translation needed.
- **Bazel Code Snippet**:
```starlark
# No direct Bazel equivalent - installation handled by packaging rules
```
- **Output Bazel File**: N/A (no direct translation)
- **References**: [PROPOSED-BAP-CMAKE-MODULE-001] Module Import, [TODO] Bazel Installation Directories

## Variable Configuration

### [BA-CMAKE-VARIABLE-001] Version Variable Definition
- **Description**: Defines a CMake variable to store the library version number, which is used later for setting library properties.

- **Source Code Snippet**:
```cmake
set(PARSON_VERSION 1.5.3)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 6
- **Bazel Mapping Description**: Bazel can use variables through simple variable assignments. Version information can be stored in variables and referenced throughout the BUILD file.
- **Bazel Code Snippet**:
```starlark
PARSON_VERSION = "1.5.3"
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-CMAKE-VARIABLE-001] Variable Definition, [TODO] Bazel Variable Usage

## Library Target Configuration

### [BA-CMAKE-LIBRARY-001] Library Target Creation
- **Description**: Creates a library target named "parson". This defines a build target that will produce a library artifact and can be referenced by other CMake commands and targets.

- **Source Code Snippet**:
```cmake
add_library(parson parson.c)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 7
- **Bazel Mapping Description**: Bazel creates library targets using cc_library rule with specified source files and headers.
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
- **References**: [PROPOSED-BAP-CMAKE-LIBRARY-001] Library Target Creation, [TODO] Bazel Library Target

### [BA-CMAKE-LIBRARY-002] Library Source File Assignment
- **Description**: Assigns the source file parson.c to the parson library target. This specifies which source files will be compiled to create the library artifact.

- **Source Code Snippet**:
```cmake
add_library(parson parson.c)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 7
- **Bazel Mapping Description**: Bazel source file assignment is handled directly in the srcs attribute of cc_library rule. No separate assignment step needed as sources are specified inline.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],  # Source files assigned directly
    hdrs = ["parson.h"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-CMAKE-LIBRARY-002] Library Source File Assignment, [TODO] Bazel Source Assignment

## Include Directory Configuration

### [BA-CMAKE-INCLUDE-001] Target Include Directory Configuration
- **Description**: Configures the include directories for the parson library target, specifically setting up the interface include directory for installation. The generator expression ensures the include path is only applied during installation.

- **Source Code Snippet**:
```cmake
target_include_directories(parson PUBLIC $<INSTALL_INTERFACE:include>)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 8
- **Bazel Mapping Description**: Bazel include directories are handled implicitly through hdrs attribute and strip_include_prefix. The INSTALL_INTERFACE generator expression has no direct Bazel equivalent as installation is handled differently.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],
    # Include paths are implicit from hdrs attribute
    # Installation interface handling not directly mapped
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-CMAKE-INCLUDE-001] Target Include Directory Configuration, [TODO] Bazel Include Path Management

## Target Properties Configuration

### [BA-CMAKE-PROPERTIES-001] Public Header Property Setting
- **Description**: Sets the PUBLIC_HEADER property on the parson target to specify which header file should be installed as part of the public interface.

- **Source Code Snippet**:
```cmake
set_target_properties(parson PROPERTIES PUBLIC_HEADER "parson.h")
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 10
- **Bazel Mapping Description**: Bazel public headers are specified directly in the hdrs attribute of cc_library. No separate property setting needed as headers are declared inline with the target.
- **Bazel Code Snippet**:
```starlark
cc_library(
    name = "parson",
    srcs = ["parson.c"],
    hdrs = ["parson.h"],  # Public headers specified directly
    visibility = ["//visibility:public"],
)
```
- **Output Bazel File**: BUILD.bazel
- **References**: [PROPOSED-BAP-CMAKE-PROPERTIES-002] Public Header Property Setting, [TODO] Bazel Header Declaration

### [BA-CMAKE-PROPERTIES-002] Library Version Property Setting
- **Description**: Sets the VERSION property on the parson target to specify the library version for shared library naming conventions.

- **Source Code Snippet**:
```cmake
set_target_properties(parson PROPERTIES VERSION ${PARSON_VERSION})
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 11
- **Bazel Mapping Description**: Bazel doesn't have direct equivalent for library VERSION property. Shared library versioning is handled through different mechanisms or external packaging tools. Version information can be embedded as defines if needed.
- **Bazel Code Snippet**:
```starlark
# No direct VERSION property equivalent in Bazel
# Version can be embedded as preprocessor define if needed:
# defines = ["PARSON_VERSION=\\\"{}\\\"".format(PARSON_VERSION)]
```
- **Output Bazel File**: N/A (no direct equivalent)
- **References**: [PROPOSED-BAP-CMAKE-PROPERTIES-001] Target Property Setting, [TODO] Bazel Library Versioning

### [BA-CMAKE-PROPERTIES-003] Library SOVERSION Property Setting
- **Description**: Sets the SOVERSION property on the parson target to specify the shared library ABI version for compatibility management.

- **Source Code Snippet**:
```cmake
set_target_properties(parson PROPERTIES SOVERSION ${PARSON_VERSION})
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 12
- **Bazel Mapping Description**: Bazel doesn't have direct equivalent for SOVERSION property. ABI versioning and shared library compatibility management are handled through different mechanisms or external tools. Not directly mapped in BUILD files.
- **Bazel Code Snippet**:
```starlark
# No direct SOVERSION property equivalent in Bazel
# ABI versioning handled through external mechanisms
```
- **Output Bazel File**: N/A (no direct equivalent)
- **References**: [PROPOSED-BAP-CMAKE-PROPERTIES-001] Target Property Setting, [TODO] Bazel ABI Versioning

## Installation Configuration

### [BA-CMAKE-INSTALL-001] Target Installation Configuration
- **Description**: Configures the installation of the parson library target, specifying different destinations for runtime, library, archive, and public header components. Also exports the target for use by other CMake projects.

- **Source Code Snippet**:
```cmake
install(
    TARGETS parson
    EXPORT parsonTargets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} COMPONENT shlib
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 14-21
- **Bazel Mapping Description**: Bazel installation and packaging mapping skipped for now. Will be addressed in future packaging-focused assessment.
- **Bazel Code Snippet**:
```starlark
# Packaging mapping deferred - to be addressed separately
```
- **Output Bazel File**: N/A (packaging mapping deferred)
- **References**: [PROPOSED-BAP-CMAKE-INSTALL-001] Target Installation Configuration, [TODO] Bazel Installation and Packaging

### [BA-CMAKE-INSTALL-002] Export Installation Configuration
- **Description**: Installs the exported targets configuration file, which allows other CMake projects to find and use the parson library through find_package(). Creates a CMake config file with namespace support.

- **Source Code Snippet**:
```cmake
install(
    EXPORT parsonTargets
    FILE parsonConfig.cmake
    NAMESPACE parson::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)
```

- **Path to Source File**: CMakeLists.txt
- **Line Number in Source File**: 23-28
- **Bazel Mapping Description**: Bazel doesn't have direct equivalent for CMake export installation and config file generation. External project discovery is handled through different mechanisms like MODULE.bazel dependencies or manual integration. No direct BUILD file mapping needed.
- **Bazel Code Snippet**:
```starlark
# No direct equivalent for CMake export/config file generation
# External project integration handled through MODULE.bazel or manual setup
```
- **Output Bazel File**: N/A (no direct equivalent)
- **References**: [PROPOSED-BAP-CMAKE-INSTALL-002] Export Installation Configuration, [TODO] Bazel External Project Integration
