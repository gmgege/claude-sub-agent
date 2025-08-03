---
description: "Save current workflow state with progress tracking and metadata"
allowed-tools: ["Task", "Read", "Write", "Edit", "Glob", "Grep", "TodoWrite"]
---

# Agent Workflow State Save - Persist Current Progress

Save the current agent workflow state to persistent storage with comprehensive metadata and progress tracking.

## Usage

```bash
# Save current active workflow state
/agent-workflow-state-save

# Save specific workflow by ID  
/agent-workflow-state-save <WORKFLOW_ID>

# Save with custom status
/agent-workflow-state-save <WORKFLOW_ID> --status=paused

# Force save (overwrite without backup)
/agent-workflow-state-save <WORKFLOW_ID> --force

# Save with custom checkpoint message
/agent-workflow-state-save <WORKFLOW_ID> --message="Before major refactor"
```

## Context

- Target workflow: $ARGUMENTS (or auto-detect current)
- Save current state to persistent storage
- Update progress tracking and metadata
- Create backup if state already exists

## Your Role

You are the Workflow State Persistence Specialist. You will use the workflow-state-manager agent to save the current workflow state with all progress information, artifacts, and metadata.

## Save Process

Execute the following steps to save workflow state:

### 🔍 Step 1: State Detection & Validation

Identify the target workflow and validate current state:

```javascript
// Auto-detect current workflow if no ID provided
const workflowId = '$ARGUMENTS' || detectCurrentWorkflow();

// Validate workflow exists and is accessible
const currentState = loadCurrentState(workflowId);
if (!currentState) {
  throw new Error(`Workflow not found or inaccessible: ${workflowId}`);
}

// Check for unsaved changes
const hasUnsavedChanges = detectUnsavedChanges(workflowId);
```

### 📊 Step 2: Progress Assessment

Assess current progress and gather metadata:

```javascript
// Calculate current progress
const progressInfo = calculateProgress(currentState);

// Gather phase information
const phaseStatus = assessPhaseStatus(currentState);

// Collect artifacts and outputs
const artifacts = collectArtifacts(currentState);

// Session information
const sessionInfo = gatherSessionInfo();
```

### 💾 Step 3: State Persistence

Save state using the workflow-state-manager:

```javascript
// Update state metadata
const updatedState = {
  ...currentState,
  lastUpdated: new Date().toISOString(),
  progress: progressInfo,
  phases: phaseStatus,
  artifacts: artifacts,
  session: sessionInfo
};

// Save state with backup
const saveResult = saveWorkflowState(updatedState, {
  backup: true,
  validate: true,
  updateIndex: true
});
```

### ✅ Step 4: Verification & Reporting

Verify save success and report status:

```javascript
// Verify saved state integrity
const verification = verifyStateSave(workflowId);

// Update global index
updateWorkflowIndex(workflowId, updatedState);

// Generate save report
const report = generateSaveReport(saveResult, verification);
```

## Save Options

### Status Override
- `--status=active`: Mark as active workflow
- `--status=paused`: Mark as paused/suspended
- `--status=completed`: Mark as completed
- `--status=failed`: Mark as failed

### Save Behavior
- `--force`: Skip backup creation and force overwrite
- `--no-backup`: Don't create backup of existing state
- `--validate`: Run full validation before saving
- `--compress`: Compress large artifact data

### Metadata Options
- `--message="text"`: Add checkpoint message
- `--tags="tag1,tag2"`: Add custom tags
- `--priority=high`: Set save priority level

## Error Handling

Handle various save scenarios:

### Disk Space Issues
```
❌ Insufficient disk space for state save
💡 Available: 150MB, Required: 200MB
🧹 Run /agent-workflow-state-clean to free space
```

### Permission Problems
```
❌ Permission denied: workflow-states/active/
🔧 Check directory permissions and ownership
💡 Ensure write access to workflow-states directory
```

### Corruption Detection
```
❌ State corruption detected during save
🔧 Attempting automatic recovery...
✅ Recovery successful - state saved with warnings
```

### Concurrent Access
```
⚠️ Another process is accessing this workflow
⏳ Waiting for lock release... (timeout: 30s)
💾 State saved successfully after wait
```

## Implementation Steps

1. **Parse Arguments**: Extract workflow ID and options from command
2. **Detect Workflow**: Auto-detect current workflow if not specified
3. **Load Current State**: Read existing state or create new one
4. **Assess Progress**: Calculate current progress and phase status
5. **Collect Artifacts**: Gather all generated files and outputs
6. **Prepare State**: Update metadata and prepare for persistence
7. **Save State**: Use workflow-state-manager to persist state
8. **Verify Save**: Confirm successful save and data integrity
9. **Update Index**: Update global workflow index
10. **Report Status**: Provide detailed save confirmation

## Expected Output

```
💾 Saving Workflow State

📋 Workflow Details:
  🆔 ID: workflow_dev_platform_1691234567890
  📝 Feature: 个人开发者需求调研分析平台
  🎯 Current Phase: spec-developer
  📈 Progress: 2/5 phases completed (40%)
  🔁 Iteration: 1/3

📊 Current Status:
  ✅ spec-analyst: Completed (100%)
  ✅ spec-architect: Completed (95%)
  🔄 spec-developer: In Progress (60%)
  ⏳ spec-validator: Pending
  ⏳ spec-tester: Pending

📁 Artifacts Found:
  📄 requirements.md (2.1KB)
  📄 architecture.md (4.7KB)
  📂 src/ (12 files, 45.3KB)
  📂 docs/ (5 files, 8.9KB)

💾 Save Operation:
  📍 Location: ./workflow-states/active/workflow_dev_platform_1691234567890.json
  💿 State Size: 15.2KB
  🔒 Backup Created: workflow_dev_platform_1691234567890.json.bak
  ✅ Validation: PASSED
  📊 Index Updated: YES

⏰ Save Completed: 2024-08-03 15:30:45 UTC
🎯 Next Action: Continue with spec-developer phase

✅ Workflow state saved successfully!
```

---

## Execute Save

**Workflow ID**: $ARGUMENTS

Initiating workflow state save using the workflow-state-manager agent...

### 🔍 Phase 1: State Detection

First detect and validate the target workflow:
- Parse command arguments for workflow ID and options
- Auto-detect current workflow if no ID provided
- Load existing state and validate accessibility
- Check for any unsaved changes or conflicts

### 📊 Phase 2: Progress Assessment  

Then assess current workflow progress:
- Calculate completion percentage and phase status
- Identify all generated artifacts and outputs
- Gather session information and metadata
- Validate state consistency and integrity

### 💾 Phase 3: State Persistence

Finally persist the state to storage:
- Update metadata with current timestamp and progress
- Create backup of existing state if present
- Save state file to appropriate directory (active/paused/etc)
- Update global workflow index and verification

**Begin save process now and report detailed status after completion.**