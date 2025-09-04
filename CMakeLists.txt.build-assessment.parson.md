# Build Assessment and Bazel Mapping

This document maps each significant build construct from CMakeLists.txt to its Bazel equivalent for the parson JSON parsing library.

## Contents

- **Source Code Snippet**:
```cmake
cmake_minimum_required(VERSION 3.5)
project(parson C)
```

- **Description**: Sets the minimum required CMake version to 3.5 and defines the project named "parson" using the C programming language. This establishes the project context and ensures CMake compatibility.
- **Bazel Mapping**:
  - **Description**: Bazel doesn't have a direct equivalent for minimum version requirements within BUILD files, but the Bazel version requirement is specified in a .bazelversion file at the workspace root. The project name and language are implicit in Bazel's target definitions.
  - **Output Bazel File**: .bazelversion
  - **Code Snippet**:
    ```
    <BAZEL_VERSION>
    ```
  - **Description**: C language support is handled by loading the rules_cc ruleset in the MODULE.bazel file (for Bazel 6.0+) or WORKSPACE file (for earlier versions).
  - **Output Bazel File**: MODULE.bazel
  - **Code Snippet**:
    ```starlark
    bazel_dep(name = "rules_cc", version = "<RULES_CC_VERSION>")
    ```

---

- **Source Code Snippet**:
```cmake
include (GNUInstallDirs)
```

- **Description**: Includes CMake's GNUInstallDirs module which provides standard installation directory variables like CMAKE_INSTALL_LIBDIR, CMAKE_INSTALL_INCLUDEDIR, etc., following GNU conventions.
- **Bazel Mapping**: N/A. Bazel doesn't use a separate installation phase like CMake. Instead, Bazel manages outputs directly through its build and package rules. Installation directories are handled at the packaging level through rules like pkg_tar or container_image, not at the target definition level.

---

- **Source Code Snippet**:
```cmake
set(PARSON_VERSION 1.5.3)
```

- **Description**: Defines a CMake variable containing the project version number. This variable can be used throughout the build system for version-dependent operations.
- **Bazel Mapping**:
  - **Description**: Bazel handles versioning through workspace rules and can use version information in target definitions. Version can be defined as a workspace variable or used directly in target attributes.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    # Version can be defined as a constant
    PARSON_VERSION = "1.5.3"
    ```

---

- **Source Code Snippet**:
```cmake
add_library(parson parson.c)
```

- **Description**: Creates a library target named "parson" from the source file parson.c. By default, this creates a static library unless BUILD_SHARED_LIBS is set.
- **Bazel Mapping**:
  - **Description**: Bazel uses the cc_library rule from rules_cc to create C/C++ libraries. This rule can produce both static and shared libraries depending on linkage configuration.
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
target_include_directories(parson PUBLIC $<INSTALL_INTERFACE:include>)
```

- **Description**: Sets the include directories for the parson target. The generator expression $<INSTALL_INTERFACE:include> specifies that when the library is installed and used by other projects, they should include from the "include" directory.
- **Bazel Mapping**:
  - **Description**: Bazel handles include directories through the includes attribute and header visibility. The includes attribute adds directories to the compilation include path for both the target and its consumers.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_library(
        name = "parson",
        srcs = ["parson.c"],
        hdrs = ["parson.h"],
        includes = ["."],
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

- **Description**: Sets target properties for the parson library: PUBLIC_HEADER specifies which header file should be installed for public use, VERSION sets the library version, and SOVERSION sets the shared object version for shared libraries.
- **Bazel Mapping**:
  - **Description**: Bazel handles public headers through the hdrs attribute in cc_library. Version information can be embedded using linkopts for shared libraries or through version scripts.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    cc_library(
        name = "parson",
        srcs = ["parson.c"],
        hdrs = ["parson.h"],
        linkopts = select({
            "//conditions:default": [
                "-Wl,-soname,libparson.so.1.5.3",
            ],
        }),
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

- **Description**: Installs the parson library target to standard system directories. EXPORT creates an export set for the target, RUNTIME/LIBRARY/ARCHIVE destinations specify where different library types are installed, and PUBLIC_HEADER specifies where header files are installed.
- **Bazel Mapping**:
  - **Description**: Bazel doesn't have a built-in install mechanism like CMake. Instead, installation is typically handled through packaging rules or custom deployment scripts. Libraries are consumed directly from the Bazel build tree.
  - **Output Bazel File**: BUILD.bazel
  - **Code Snippet**:
    ```starlark
    # For packaging, you would use rules like pkg_tar
    load("@rules_pkg//:pkg.bzl", "pkg_tar")
    
    pkg_tar(
        name = "parson_package",
        srcs = [":parson"],
        mode = "0644",
        package_dir = "/usr/lib",
    )
    
    pkg_tar(
        name = "parson_headers",
        srcs = ["parson.h"],
        mode = "0644", 
        package_dir = "/usr/include",
    )
    ```

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

- **Description**: Installs the export set as a CMake configuration file that other CMake projects can use to find and link against the parson library. The NAMESPACE adds a prefix to target names, and the file is installed to a standard CMake package location.
- **Bazel Mapping**:
  - **Description**: Bazel doesn't use separate configuration files for dependency management. Dependencies are managed through the MODULE.bazel system (Bazel 6.0+) or WORKSPACE files. External projects consume Bazel targets directly through their labels.
  - **Output Bazel File**: MODULE.bazel
  - **Code Snippet**:
    ```starlark
    # For projects wanting to use parson as a dependency:
    bazel_dep(name = "parson", version = "1.5.3")
    ```