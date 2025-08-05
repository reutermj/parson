# CMakeLists.txt Bazel Mapping Runbook Proposals

This document proposes new bazel mapping patterns based on the CMakeLists.txt build assessment for the parson library project.

## Proposed Bazel Mapping Patterns

### [PROPOSED-BMP-CMAKE-VERSION-001] Build System Version Requirement to Bazel Version Management
- **Pattern Description**: Maps CMake minimum version requirements to Bazel's implicit version compatibility system, where version compatibility is managed by the Bazel binary itself rather than explicit declarations in build files.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-VERSION-001] Build System Version Requirement

- **Source Example**:
```cmake
cmake_minimum_required(VERSION 3.5)
```

- **Bazel Mapping Description**: Bazel doesn't have explicit version requirements in build files. Version compatibility is managed by the Bazel binary version itself and bazel_dep version constraints in MODULE.bazel. No direct translation needed in BUILD files.

- **Bazel Code Example**:
```starlark
# No direct Bazel equivalent - version compatibility handled by Bazel binary
```

- **Output Bazel File**: N/A (no direct translation)

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-PROJECT-001] Project Declaration to Module Declaration
- **Pattern Description**: Maps CMake project() declarations to Bazel MODULE.bazel module() declarations for establishing project identity and metadata.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-PROJECT-001] Project Name Declaration

- **Source Example**:
```cmake
project(library_name C)
```

- **Bazel Mapping Description**: Bazel project identity is established through MODULE.bazel file with module() declaration. The project name becomes the module name, and language specification is handled through bazel_dep on appropriate rulesets.

- **Bazel Code Example**:
```starlark
module(
    name = "library_name",
    version = "1.0.0",
)

bazel_dep(name = "rules_cc", version = "X.Y.Z")
```

- **Output Bazel File**: MODULE.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-VARIABLE-001] Variable Definition to Bazel Variable Assignment
- **Pattern Description**: Maps CMake set() variable definitions to simple Bazel variable assignments using Starlark syntax for storing reusable values throughout the build file.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-VARIABLE-001] Variable Definition

- **Source Example**:
```cmake
set(LIBRARY_VERSION 1.0.0)
```

- **Bazel Mapping Description**: Bazel can use variables through simple variable assignments using Starlark syntax. These variables can be referenced throughout the BUILD file and used in rule attributes.

- **Bazel Code Example**:
```starlark
LIBRARY_VERSION = "1.0.0"

# Usage in rules:
# defines = ["LIBRARY_VERSION=\\\"{}\\\"".format(LIBRARY_VERSION)]
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-LIBRARY-001] Library Target Creation to CC Library Rule
- **Pattern Description**: Maps CMake add_library() commands to Bazel cc_library rules for creating library targets with specified source files and headers.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-LIBRARY-001] Library Target Creation

- **Source Example**:
```cmake
add_library(mylib mylib.c)
```

- **Bazel Mapping Description**: Bazel creates library targets using cc_library rule with specified source files in srcs attribute and headers in hdrs attribute. Public visibility should be set for libraries intended to be used by other targets.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "mylib",
    srcs = ["mylib.c"],
    hdrs = ["mylib.h"],
    visibility = ["//visibility:public"],
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-INCLUDE-001] Target Include Directories to Implicit Header Management
- **Pattern Description**: Maps CMake target_include_directories() commands to Bazel's implicit include path management through hdrs attribute and strip_include_prefix settings.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-INCLUDE-001] Target Include Directory Configuration

- **Source Example**:
```cmake
target_include_directories(mylib PUBLIC $<INSTALL_INTERFACE:include>)
```

- **Bazel Mapping Description**: Bazel include directories are handled implicitly through hdrs attribute. The hdrs files are automatically made available to dependent targets. Generator expressions like INSTALL_INTERFACE have no direct equivalent as installation is handled differently in Bazel.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "mylib",
    srcs = ["mylib.c"],
    hdrs = ["mylib.h"],
    # Include paths are implicit from hdrs attribute
    # strip_include_prefix can be used if needed for path manipulation
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-PROPERTIES-001] Target Properties to Rule Attributes
- **Pattern Description**: Maps CMake set_target_properties() commands to appropriate Bazel rule attributes, with some properties having no direct equivalent in Bazel's build model.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-PROPERTIES-001] Target Property Setting

- **Source Example**:
```cmake
set_target_properties(mylib PROPERTIES 
    PUBLIC_HEADER "mylib.h"
    VERSION ${LIBRARY_VERSION}
    SOVERSION ${LIBRARY_VERSION}
)
```

- **Bazel Mapping Description**: PUBLIC_HEADER maps to hdrs attribute in cc_library. VERSION and SOVERSION properties have no direct Bazel equivalents as shared library versioning is handled through different mechanisms or external packaging tools.

- **Bazel Code Example**:
```starlark
cc_library(
    name = "mylib",
    srcs = ["mylib.c"],
    hdrs = ["mylib.h"],  # PUBLIC_HEADER equivalent
    # VERSION/SOVERSION have no direct mapping
    # Can embed version as preprocessor define if needed:
    # defines = ["LIBRARY_VERSION=\\\"{}\\\"".format(LIBRARY_VERSION)]
)
```

- **Output Bazel File**: BUILD.bazel

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-MODULE-001] Module Include to Implicit Bazel Functionality
- **Pattern Description**: Maps CMake include() module imports to Bazel's implicit functionality or external rule dependencies, as many CMake modules provide functionality that's built into Bazel or handled differently.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-MODULE-001] Module Import

- **Source Example**:
```cmake
include(GNUInstallDirs)
```

- **Bazel Mapping Description**: Many CMake modules like GNUInstallDirs provide functionality that's either built into Bazel or handled through different mechanisms (packaging rules, command-line flags). Most module includes have no direct BUILD file translation.

- **Bazel Code Example**:
```starlark
# No direct equivalent for most CMake module includes
# Functionality handled through Bazel's built-in systems or external rules
```

- **Output Bazel File**: N/A (no direct translation)

- **Exceptional Cases**: None.

### [PROPOSED-BMP-CMAKE-INSTALL-001] Installation Configuration to Packaging Deferral
- **Pattern Description**: Maps CMake install() commands to deferred packaging solutions, as Bazel handles installation and packaging through separate mechanisms not typically defined in BUILD files.

- **Build System Pattern Identification**: [PROPOSED-BAP-CMAKE-INSTALL-001] Target Installation Configuration

- **Source Example**:
```cmake
install(
    TARGETS mylib
    EXPORT mylibTargets
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)
```

- **Bazel Mapping Description**: Bazel installation and packaging are typically handled through separate packaging rules (pkg_tar, pkg_deb, etc.) or external tools rather than direct BUILD file declarations. This mapping is deferred for packaging-focused assessment.

- **Bazel Code Example**:
```starlark
# Installation and packaging mapping deferred
# To be addressed through specialized packaging rules
```

- **Output Bazel File**: N/A (packaging mapping deferred)

- **Exceptional Cases**: None.
