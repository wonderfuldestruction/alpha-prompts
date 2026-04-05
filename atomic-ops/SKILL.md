---
name: atomic-ops
description: Execute batched repository operations via a single Python script using execute_code
---

# Atomic Operations (Hermes Agent)

Execute deterministic repository changes via a single `execute_code` tool call containing a Python script. All operations (discovery, reading, editing, verification) happen inside the Python runtime, avoiding sequential tool call overhead.

## Core Mandate

**One tool call per response**: Use `execute_code` with a Python script that handles the entire pipeline.

**DO NOT** make sequential tool calls like `read_file` → `read_file` → `write_file`. This is the anti-pattern this skill exists to prevent.

**DO** use `execute_code` with `from hermes_tools import terminal, read_file, write_file, search_files, patch` and batch all operations inside the Python script.

## Python Script Structure

### PHASE 0: Snapshot
```python
repo_root = "/path/to/repo"
status = terminal(command="git status --porcelain", workdir=repo_root)
print(f"Repo: {repo_root}")
```

### PHASE 1: Discovery
```python
# Use search_files or parse terminal output
files = search_files(pattern="*.py", path=repo_root)['matches']
# Or parse git status output
lines = status['output'].strip().split('\n')
files = [line[3:].strip() for line in lines if line.startswith('??') or line.startswith(' M')]
```

### PHASE 2: Process
```python
for filepath in files:
    content = read_file(path=filepath)
    # Process content here
    # Use patch() for edits or write_file() for new files
```

### PHASE 3: Validate
```python
# Run compiler/test commands via terminal tool
result = terminal(command="cargo check", workdir=repo_root)
if result['exit_code'] != 0:
    print(f"Validation failed: {result['output']}")
    exit(1)
```

### PHASE 4: Summary
```python
print(f"Files scanned: {len(files)}")
print(f"Files changed: {changed_count}")
print("Validation: PASSED")
```

## Tooling Strategy

Import from `hermes_tools`:
- `terminal` - run shell commands
- `read_file` - read file contents
- `write_file` - write new files
- `patch` - targeted find-and-replace edits
- `search_files` - find files by pattern
- `json_parse` - parse structured output

## Discovery Rules

- Use `search_files` for pattern-based discovery (faster than `find`)
- Exclude junk dirs: `node_modules`, `dist`, `build`, `.git`, `target`, `.venv`
- Always sort and dedupe file lists
- Never print full file contents unless explicitly requested

## Anti-Patterns

- **Sequential tool calls**: Making multiple `read_file` → `write_file` calls. If you're making more than one tool call, you're NOT using atomic-ops correctly.
- Unbounded repo scans that escape the project directory
- Assuming file paths exist without discovery
- Continuing after validation failure

## Example: Summarize Unstaged Files

```python
from hermes_tools import terminal, read_file, write_file

repo_root = "/path/to/repo"
status = terminal(command="git status --porcelain", workdir=repo_root)

files = [line[3:].strip() for line in status['output'].strip().split('\n') 
         if line.startswith('??') or line.startswith(' M')]

summaries = []
for f in files:
    content = read_file(path=f"{repo_root}/{f}")
    first_line = content['content'].strip().split('\n')[0]
    summaries.append(f"- **{f}**: {first_line}")

write_file(path=f"{repo_root}/summary.md", content="\n".join(summaries))
print(f"Processed {len(files)} files")
```