# Parson CMakeLists.txt Build Assessment and Bazel Mapping

- **Source Code Snippet**:
```cmake
cmake_minimum_required(VERSION 3.5)
```

- **Description**: Establishes the minimum CMake version requirement (3.5) needed to process this build script. This ensures compatibility and access to specific CMake features introduced in that version.
- **Bazel Mapping**:
  - **Description**: Bazel version requirements are specified in .bazelversion file at the workspace root
  - **Output Bazel File**: .bazelversion
  - **Code Snippet**:
    ```plaintext
    <BAZEL_VERSION>
    ```

---

- **Source Code Snippet**:
```cmake
project(parson C)
```

- **Description**: Declares the project name as "parson" and specifies that it uses the C programming language. This enables CMake's C compiler detection and sets up the build environment for C compilation.
- **Bazel Mapping**:
  - **Description**: Project name and language support are handled through MODULE.bazel declaration and rules_cc dependency for C/C++ compilation
  - **Output Bazel File**: MODULE.bazel
  - **Code Snippet**:
    ```starlark
    module(name = "parson", version = "1.5.3")
    
    bazel_dep(name = "rules_cc", version = "<RULES_CC_VERSION>")
    ```

---

- **Source Code Snippet**:
```cmake
include (GNUInstallDirs)
```

- **Description**: Includes CMake's GNUInstallDirs module which provides standard installation directory variables like CMAKE_INSTALL_BINDIR, CMAKE_INSTALL_LIBDIR, etc. for Unix-like systems.
- **Bazel Mapping**: N/A. Bazel handles installation through different mechanisms (bazel run for executables, manual copying for libraries, or packaging rules). Installation directories are not configured at build-time but rather handled by deployment scripts or packaging rules.

---

- **Source Code Snippet**:
```cmake
set(PARSON_VERSION 1.5.3)
```

- **Description**: Defines a CMake variable containing the project version number that can be referenced throughout the build script.
- **Bazel Mapping**:
  - **Description**: Variables can be defined directly in BUILD.bazel files using standard Starlark variable assignment for use within that build file
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    PARSON_VERSION = "1.5.3"
    ```

---

- **Source Code Snippet**:
```cmake
add_library(parson parson.c)
```

- **Description**: Creates a library target named "parson" from the source file parson.c. By default, this creates a static library, but CMake can build shared libraries based on BUILD_SHARED_LIBS variable.
- **Bazel Mapping**:
  - **Description**: Use cc_library rule to create both static and shared library targets from C source files
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    load("@rules_cc//cc:defs.bzl", "cc_library")
    
    cc_library(
        name = "parson",
        srcs = ["parson.c"],
        hdrs = ["parson.h"],
        visibility = ["//visibility:public"],
    )
    ```

---

- **Source Code Snippet**:
```cmake
target_include_directories(parson PUBLIC $<INSTALL_INTERFACE:include>)
```

- **Description**: Adds include directories to the parson target. The generator expression $<INSTALL_INTERFACE:include> specifies that when this library is installed and used by other projects, they should include the "include" directory.
- **Bazel Mapping**:
  - **Description**: Bazel automatically handles include paths through hdrs and includes attribute for public headers
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_library(
        name = "parson",
        srcs = ["parson.c"],
        hdrs = ["parson.h"],
        includes = [".],
        visibility = ["//visibility:public"],
    )
    ```

---

- **Source Code Snippet**:
```cmake
set_target_properties(parson PROPERTIES PUBLIC_HEADER "parson.h")
set_target_properties(parson PROPERTIES VERSION ${PARSON_VERSION})
set_target_properties(parson PROPERTIES SOVERSION ${PARSON_VERSION})
```

- **Description**: Sets target properties for the parson library: designates parson.h as a public header for installation, sets the library version, and sets the shared object version (SOVERSION) for shared library compatibility.
- **Bazel Mapping**:
  - **Description**: Public headers are specified in hdrs attribute of cc_library. Library versioning in Bazel is handled through target naming or custom rules, as Bazel focuses on hermetic builds rather than system-wide library versioning
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_library(
        name = "parson",
        srcs = ["parson.c"],
        hdrs = ["parson.h"],
        visibility = ["//visibility:public"],
    )
    ```

---

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

- **Description**: Installs the parson library target to system directories. Specifies different destinations for runtime libraries (shared libs on Windows), library files (shared libs on Unix), archive files (static libs), and public headers. Also exports the target for use by other CMake projects.
- **Bazel Mapping**: N/A. Bazel does not have a direct equivalent to CMake's install() command. Installation is typically handled through custom deployment scripts, packaging rules (like rules_pkg), or manual copying of bazel-bin outputs. Bazel's philosophy emphasizes hermetic builds over system-wide installation.

---

- **Source Code Snippet**:
```cmake
install(
    EXPORT parsonTargets
    FILE parsonConfig.cmake
    NAMESPACE parson::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/${PROJECT_NAME}
)
```

- **Description**: Installs a CMake configuration file that allows other CMake projects to find and link against the parson library using find_package(parson). Creates parsonConfig.cmake with parson:: namespace for target imports.
- **Bazel Mapping**: N/A. Bazel uses a different dependency management system based on MODULE.bazel (Bzlmod) or WORKSPACE files. External dependencies are declared in MODULE.bazel and resolved through Bazel's module system rather than through config files. Projects consuming this library would add it as a bazel_dep in their MODULE.bazel.
