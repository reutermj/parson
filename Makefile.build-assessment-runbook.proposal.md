# Makefile Build Assessment Runbook Proposal

This document proposes build assessment patterns for Makefile build systems based on analysis of the parson library project.

## Proposal Overview
Based on the Makefile build assessment, the following patterns have been identified as fundamental constructs that appear in Makefile build systems and require standardized assessment approaches.

## Proposed Build Assessment Patterns

### Variable Configuration

#### [PROPOSED-BAP-MAKE-VARIABLE-001] Variable Definition
- **Pattern Description**: Defines a variable that stores a value for reuse throughout the Makefile. Variables provide configuration control and avoid repetition of common values like compiler names, flags, or paths.

- **Pattern Identification**: 
  - Variable assignment syntax with `=` operator
  - Variable name on left side of assignment
  - Value on right side of assignment
  - May be overridden from command line or environment

- **Source Example**:
```makefile
VARIABLE_NAME = value
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MAKE-VARIABLE-002] Compiler Selection Variable
- **Pattern Description**: Defines a variable that specifies which compiler to use for building source files. This allows easy switching between different compilers and provides a central configuration point.

- **Pattern Identification**: 
  - Variable name indicating compiler purpose (CC, CXX, etc.)
  - Value containing compiler executable name
  - Referenced in compilation targets using $(VARIABLE) syntax

- **Source Example**:
```makefile
CC = gcc
CXX = g++
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MAKE-VARIABLE-003] Compiler Flags Variable
- **Pattern Description**: Defines a variable that contains compilation flags to be passed to the compiler. This centralizes compiler configuration and allows easy modification of build settings.

- **Pattern Identification**: 
  - Variable name indicating flags purpose (CFLAGS, CXXFLAGS, etc.)
  - Value containing space-separated compiler flags
  - Referenced in compilation commands using $(VARIABLE) syntax
  - May include optimization, debugging, warning, and standard compliance flags

- **Source Example**:
```makefile
CFLAGS = -Wall -Wextra -std=c99 -O2
```

- **Exceptional Cases**: None.

### Target Configuration

#### [PROPOSED-BAP-MAKE-TARGET-001] Target Definition
- **Pattern Description**: Defines a build target that specifies dependencies and commands to execute. Targets represent buildable artifacts or actions that can be invoked individually or as dependencies of other targets.

- **Pattern Identification**: 
  - Target name followed by colon
  - Optional dependency list after colon
  - Indented command lines (using tabs) following target line
  - Commands may reference variables and automatic variables

- **Source Example**:
```makefile
mytarget: dependency1 dependency2
	command1
	command2
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MAKE-TARGET-002] Executable Build Target
- **Pattern Description**: Defines a target that compiles source files into an executable using a compiler command. This target specifies source dependencies and the compilation command with appropriate flags.

- **Pattern Identification**: 
  - Target name representing executable
  - Source file dependencies listed after colon
  - Compiler invocation command using variables like $(CC)
  - Output specification using `-o $@` or similar
  - Source files passed as command arguments

- **Source Example**:
```makefile
myprogram: source1.c source2.c
	$(CC) $(CFLAGS) -o $@ source1.c source2.c
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MAKE-TARGET-003] Test Execution Target
- **Pattern Description**: Defines a target that builds an executable and immediately runs it for testing purposes. This combines compilation and execution in a single target for convenient testing workflows.

- **Pattern Identification**: 
  - Target name indicating test purpose
  - Source file dependencies
  - Compilation command to build executable
  - Execution command using `./$@` or explicit executable name

- **Source Example**:
```makefile
test: test.c mylib.c
	$(CC) $(CFLAGS) -o $@ test.c mylib.c
	./$@
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MAKE-TARGET-004] Clean Target
- **Pattern Description**: Defines a target that removes generated files to provide a clean build environment. This is typically a phony target that uses rm commands to delete build artifacts.

- **Pattern Identification**: 
  - Target name "clean" or similar cleanup-related name
  - Command using `rm` to delete files
  - Often uses `-f` flag to ignore missing files
  - May use wildcards to match multiple files

- **Source Example**:
```makefile
clean:
	rm -f *.o myprogram
```

- **Exceptional Cases**: None.

#### [PROPOSED-BAP-MAKE-TARGET-005] Default Target
- **Pattern Description**: Defines the default target that executes when make is run without arguments. This target typically depends on other targets to build the main project artifacts.

- **Pattern Identification**: 
  - Target name "all" or appears as first target in Makefile
  - Dependencies list containing other target names
  - Usually no direct commands, relies on dependency execution
  - Represents the primary build goal

- **Source Example**:
```makefile
all: program1 program2 tests
```

- **Exceptional Cases**: None.

### Phony Target Configuration

#### [PROPOSED-BAP-MAKE-PHONY-001] Phony Target Declaration
- **Pattern Description**: Declares targets as phony, meaning they don't represent actual files but are action targets that should always be executed when requested. This prevents conflicts with files of the same name.

- **Pattern Identification**: 
  - `.PHONY:` directive
  - List of target names that should be treated as phony
  - Target names separated by spaces

- **Source Example**:
```makefile
.PHONY: clean test install
```

- **Exceptional Cases**: None.
