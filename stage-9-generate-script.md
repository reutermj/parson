# Generate and Test Bazel Build Files

This document provides instructions for generating Bazel build files from mapping assessments and testing that the generated build system produces equivalent behavior to the original build system. The goal is to create working Bazel build files that successfully compile, link, and test the project while maintaining an action log.

**Important**: This stage converts mapping specifications into actual working Bazel build files. The focus is on implementing the mappings correctly and validating that the build behavior matches the original system.

For every build system conversion project:
1. Read the existing `bazel-generation-runbook.md` to understand patterns and approaches for generating Bazel files and handling common issues.
2. Read all relevant `<file name>.build-assessment.md` to understand the required Bazel constructs and their configurations.
3. Read all relevant `<file name>.source-assessment.md` to understand the required Bazel constructs and their configurations.
4. Generate Bazel build files based on the mapping specifications.
5. Test the generated Bazel build system to validate equivalent behavior.
6. Action Log Maintenance: maintain detailed logs of all actions, expectations, outcomes, and interactions.

## Action Log (`bazel-generation-log.<project>.md`)

The action log maintains a comprehensive record of all actions taken during the generation and testing process. This serves as both a debugging tool and a knowledge capture mechanism for understanding what works and what fails during Bazel file generation.

### Action Log Guidelines
* Log every action before it is taken, including expectations
* Log actual outcomes immediately after each action
* Log all interactions with humans, including exact prompts and context
* Maintain chronological order of all entries
* Reference relevant runbook entries for each action

### Action Log Entry Contents
* For each action entry, include:
  - **Action Count**: sequential counter for this action (e.g., ACTION-001)
  - **Action Type**: category of action (e.g., FILE_GENERATION, BUILD_TEST, HUMAN_INTERACTION)
  - **Action Description**: detailed explanation of what the agent is doing
  - **Expected Outcome**: what the agent expects to happen as a result of this action
  - **Actual Outcome**: what actually happened when the action was executed
  - **Outcome Analysis**: was the outcome expected? If not, why was it unexpected?
  - **Relevant References**: runbook entries that guided this action or apply to the outcome. If no relevant action pattern exists in the runbook, write `TODO`; a later stage will fill this in.

#### Action Entry Example

````
### [ACTION-###] <ACTION_TYPE>: <TITLE>
- **Action Description**: <DETAILED DESCRIPTION OF ACTION>
- **Expected Outcome**: <WHAT THE AGENT EXPECTS TO HAPPEN>
- **Actual Outcome**: <WHAT ACTUALLY HAPPENED>
- **Outcome Analysis**: <WAS OUTCOME EXPECTED? IF NOT, WHY UNEXPECTED?>
- **Relevant References**: [GRP-<CATEGORY>-<NUMBER>], [PROPOSED-GRP-<CATEGORY>-<NUMBER>]
````

### Human Interaction Log Entry Contents
* For each human interaction entry, include:
  - **Interaction Count**: sequential counter for this interaction (e.g., HUMAN-001)
  - **Interaction Context**: detailed description of the situation that required human input
  - **Issue Description**: explanation of the specific issue that could not be resolved autonomously
  - **Exact Prompt Given**: verbatim text of the prompt provided to the human
  - **Human Response**: exact response received from the human
  - **Expected Agent Action**: what action the agent is expected to take based on the human response
  - **Relevant References**: runbook entries related to this type of issue. If no relevant action pattern exists in the runbook, write `TODO`; a later stage will fill this in.

#### Human Interaction Entry Example

````
### [HUMAN-###] <INTERACTION_TYPE>: <TITLE>
- **Interaction Context**: <DETAILED SITUATION DESCRIPTION>
- **Issue Description**: <SPECIFIC ISSUE REQUIRING HUMAN INPUT>
- **Exact Prompt Given**: "<VERBATIM PROMPT TEXT>"
- **Human Response**: "<EXACT HUMAN RESPONSE>"
- **Expected Agent Action**: <ACTION AGENT SHOULD TAKE>
- **Relevant References**: [GRP-<CATEGORY>-<NUMBER>]
````
