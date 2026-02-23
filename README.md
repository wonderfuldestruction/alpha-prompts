# Overview

This repository specializes in system prompts and context engineering, with a focus on `system-code-alpha` - a high-efficiency system prompt for code-related tasks.

## Key Advantages

- **90% reduction** in system prompting compared to traditional approaches
- **Batched tool execution** to minimize context window churn
- **Terminal-first** approach with ephemeral script execution
- **Extended session** capability through efficient context usage

## Key Disadvantages

- **Terminal execution risk**: Operating in terminal environment poses potential for destructive actions if proper guardrails are not configured in Kilocode settings
- Requires careful user steering to prevent unintended file modifications or deletions

## system-code-alpha

`system-code-alpha` is a specialized system prompt designed for code-related tasks. It has been tuned for optimal performance on the following models:

- **Primary Targets:**
  - Devstral Small 2

- **Good Performance:**
  - GLM 4.6
  - GPT-OSS models
  - Qwen3 Coder Next

- **Mixed Results:**
  - GLM 4.7 Flash (mostly a miss - likely due to quantization issues or other technical problems)

### Design Philosophy

`system-code-alpha` was specifically engineered to operate as a **Kilocode agent**, with compatibility for forks like Roo. It represents an alternative to Kilocode's original Code agent, offering significant improvements in efficiency.

### Key Features

1. **Tool Configuration**
   - Edit and write tools should be **disabled** in settings to avoid model misguidance
   - Kilocode provides guidance in the background for different active tools
   - This configuration helps save context window space and retry churn

2. **Execution Environment**
   - Primarily operates in **terminal** environment
   - Creates and executes **ephemeral scripts** for tool calls and might leave some script artifacts
   - Prioritizes **batched tool calls** to minimize tool-call churning
   - Can perform multiple operations (write/validate/test/execute) in a single turn

3. **Repository Search**
   - More effective **without index** for repo search
   - Leverages tool flexibility for efficient navigation

4. **Context Efficiency**
   - Designed to save **~90% on system prompting** compared to original Kilocode Code agent
   - Due to batching capabilities and efficient repo search, can work substantially longer in the same session
   - Significantly reduces context window bloat (original agent used ~20k tokens)

### Use Cases

`system-code-alpha` is ideal for:
- Code writing and modification tasks
- Terminal-based development workflows
- Projects requiring efficient context window usage
- Long-running sessions where context preservation is critical

### Compatibility

While optimized for Kilocode, this prompt maintains compatibility with:
- Roo (Kilocode fork)
- Various open-source code models
- Terminal-based development environments

### Safety Recommendations

To mitigate risks associated with terminal execution:
- Always enable guardrails in Kilocode settings
- Carefully review all terminal commands before execution
- Monitor sessions closely for unintended operations

