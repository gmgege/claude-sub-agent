---
description: "Load and restore workflow state from persistent storage with context recovery"
allowed-tools: ["Task", "Read", "Write", "Edit", "Glob", "Grep", "TodoWrite"]
---

# Agent Workflow State Load - Restore Development Context

Load an existing workflow state from persistent storage and restore the complete development context for continuation.

## Usage

```bash
# Load most recent active workflow
/agent-workflow-state-load

# Load specific workflow by ID
/agent-workflow-state-load <WORKFLOW_ID>

# Load with validation and repair
/agent-workflow-state-load <WORKFLOW_ID> --validate --repair

# Load as new session
/agent-workflow-state-load <WORKFLOW_ID> --new-session

# Load and set as current workflow
/agent-workflow-state-load <WORKFLOW_ID> --set-current
```

## Context

- Target workflow: $ARGUMENTS (or auto-detect most recent)
- Load state from persistent storage
- Restore development context and progress
- Validate state integrity and compatibility

## Your Role

You are the Workflow State Recovery Specialist. You will use the workflow-state-manager agent to load and restore a workflow state with full context recovery and validation.

## Load Process

Execute the following steps to load workflow state:

### 🔍 Step 1: State Discovery & Selection

Identify and validate the target workflow state:

```javascript
// Resolve workflow ID from arguments or auto-detect
const workflowId = '$ARGUMENTS' || findMostRecentActiveWorkflow();

if (!workflowId) {
  // Show available workflows for selection
  const availableWorkflows = listAvailableWorkflows();
  return promptWorkflowSelection(availableWorkflows);
}

// Validate workflow exists and is loadable
const stateExists = checkWorkflowExists(workflowId);
if (!stateExists) {
  throw new Error(`Workflow state not found: ${workflowId}`);
}
```

### 📋 Step 2: State Loading & Validation

Load the state file and perform comprehensive validation:

```javascript
// Load raw state data
const rawState = loadStateFile(workflowId);

// Validate state schema and structure
const validation = validateStateSchema(rawState);
if (!validation.isValid) {
  return handleStateCorruption(workflowId, validation.errors);
}

// Check version compatibility
const compatibility = checkVersionCompatibility(rawState);
if (!compatibility.compatible) {
  rawState = migrateState(rawState, compatibility.targetVersion);
}

// Verify artifact integrity
const artifactCheck = verifyArtifacts(rawState);
```

### 🔄 Step 3: Context Restoration

Restore the complete development context:

```javascript
// Restore workspace state
const workspace = restoreWorkspaceContext(rawState);

// Load phase artifacts and outputs
const artifacts = loadArtifacts(rawState.artifacts);

// Restore progress tracking
const progress = restoreProgressState(rawState.progress);

// Initialize session context
const session = initializeSessionContext(rawState.session);

// Prepare continuation context
const context = prepareContinuationContext(rawState, workspace, artifacts);
```

### ✅ Step 4: State Activation & Reporting

Activate the loaded state and report status:

```javascript
// Set as active workflow if requested
if (options.setCurrent) {
  setCurrentWorkflow(workflowId);
}

// Update session information
updateSessionInfo(workflowId, {
  sessionId: generateNewSessionId(),
  resumedAt: new Date().toISOString(),
  resumedFrom: rawState.session.lastCheckpoint
});

// Generate load report
const report = generateLoadReport(workflowId, rawState, context);
```

## Load Options

### Discovery Options
- `--recent`: Load most recently updated workflow
- `--active`: Load most recent active workflow
- `--pattern=<pattern>`: Search workflows by pattern

### Validation Options
- `--validate`: Run full validation before loading
- `--repair`: Attempt automatic repair of minor issues
- `--force`: Load even with validation warnings

### Session Options
- `--new-session`: Create new session ID
- `--set-current`: Set as current active workflow
- `--readonly`: Load in read-only mode

### Context Options
- `--minimal`: Load minimal context (metadata only)
- `--full`: Load complete context including artifacts
- `--artifacts`: Include artifact content in context

## Error Handling

Handle various load scenarios:

### Workflow Not Found
```
❌ Workflow not found: workflow_test_123
💡 Available workflows:
  📋 /agent-workflow-state-list
  🔍 /agent-workflow-state-list --pattern="test"
```

### State Corruption
```
❌ State file corrupted: invalid JSON structure
🔧 Attempting automatic repair...
⚠️ Partial recovery successful with data loss
💡 Manual review recommended before continuing
```

### Version Incompatibility
```
⚠️ State version mismatch: found v1.0, expected v1.2
🔄 Migrating state to compatible version...
✅ Migration successful - state loaded with warnings
```

### Missing Artifacts
```
⚠️ Some artifacts are missing or inaccessible:
  ❌ src/components/UserAuth.js (not found)
  ❌ docs/api-spec.md (permission denied)
💡 Continue with partial context or restore missing files?
```

### Lock Conflicts
```
🔒 Workflow is locked by another session
📊 Session: session_abc123 (started 15 minutes ago)
🕰️ Last activity: 2 minutes ago
💡 Force unlock? This may cause data loss in other session.
```

## State Information Display

### Workflow Overview
```
📋 Workflow Loaded Successfully

🆔 Workflow ID: workflow_dev_platform_1691234567890
📝 Feature: 个人开发者需求调研分析平台
📅 Created: 2024-08-02 10:15:30 UTC
🕐 Last Updated: 2024-08-02 14:22:15 UTC
⏰ Session Duration: 4h 7m

📊 Progress Status:
  🎯 Current Phase: spec-developer
  📈 Completion: 2/5 phases (40%)
  🔁 Iteration: 1/3
  ⭐ Quality Score: 92% (last validation)

📁 Artifacts Overview:
  📄 requirements.md (2.1KB) - 2 hours ago
  📄 architecture.md (4.7KB) - 1 hour ago  
  📂 src/ (12 files, 45.3KB) - 30 minutes ago
  📄 validation-report.md (1.2KB) - 15 minutes ago
```

### Phase Breakdown
```
📊 Phase Status Details:

✅ spec-analyst (Completed)
   ⏱️ Duration: 45 minutes
   ⭐ Score: N/A
   📁 Outputs: requirements.md, user-stories.md

✅ spec-architect (Completed)  
   ⏱️ Duration: 1h 20m
   ⭐ Score: 95%
   📁 Outputs: architecture.md, api-spec.md, tech-stack.md

🔄 spec-developer (In Progress - 60%)
   ⏱️ Duration: 2h 15m (ongoing)
   ⭐ Score: Pending
   📁 Outputs: src/ directory (12 files)
   🎯 Next: Complete user authentication module

⏳ spec-validator (Pending)
⏳ spec-tester (Pending)
```

### Session Context
```
🎮 Session Information:

📱 Session ID: session_1691234567890_resume
🔄 Resumed From: session_1691234567890_original
⏰ Resume Time: 2024-08-03 15:30:45 UTC
🕐 Original Duration: 4h 7m
⚡ Session Limits: 6h remaining

🎯 Ready to Continue:
  📍 Current Location: spec-developer phase
  🚀 Next Action: Continue implementation
  📋 Context: All artifacts and progress restored
```

## Implementation Steps

1. **Parse Arguments**: Extract workflow ID and load options
2. **Discover Workflow**: Find target workflow or prompt for selection
3. **Load State File**: Read and parse workflow state from storage
4. **Validate State**: Check schema, version, and data integrity  
5. **Migrate if Needed**: Handle version incompatibilities
6. **Verify Artifacts**: Check availability and integrity of artifacts
7. **Restore Context**: Load workspace and development context
8. **Initialize Session**: Set up new session information
9. **Activate Workflow**: Set as current if requested
10. **Report Status**: Provide detailed load confirmation and context

## Expected Output

```
🔄 Loading Workflow State

🔍 Discovery Phase:
  🎯 Target: workflow_dev_platform_1691234567890
  📍 Location: ./workflow-states/active/
  📊 State Size: 15.2KB
  ✅ File Access: OK

🔬 Validation Phase:
  📋 Schema: VALID (v1.2)
  🧮 Integrity: PASSED
  📁 Artifacts: 23/23 found
  🔗 Dependencies: RESOLVED

🔄 Context Restoration:
  📂 Workspace: Restored
  📁 Artifacts: Loaded (23 files, 61.1KB)
  📊 Progress: Restored (40% complete)
  🎯 Phase Context: spec-developer ready

✅ Workflow State Loaded Successfully!

🎮 Ready to Continue:
  📝 Feature: 个人开发者需求调研分析平台
  🎯 Phase: spec-developer (60% complete)
  🔁 Iteration: 1/3
  ⏰ Time Remaining: 6h estimated

🚀 Next Steps:
  1. Continue with user authentication implementation
  2. Complete remaining spec-developer tasks
  3. Proceed to spec-validator quality gate

💡 Use /agent-workflow-resume to continue automated execution
```

---

## Execute Load

**Workflow ID**: $ARGUMENTS

Initiating workflow state load using the workflow-state-manager agent...

### 🔍 Phase 1: State Discovery

First discover and validate the target workflow:
- Parse command arguments for workflow ID and options
- Search for target workflow across all state directories
- Validate file accessibility and basic structure
- Handle workflow selection if multiple candidates found

### 📋 Phase 2: State Validation

Then validate and prepare the state for loading:
- Load state file and validate JSON structure
- Check schema compatibility and perform migrations if needed
- Verify artifact availability and integrity
- Detect any corruption or missing dependencies

### 🔄 Phase 3: Context Restoration

Finally restore the complete development context:
- Load all artifacts and workspace configuration
- Restore progress tracking and phase information
- Initialize new session context for continuation
- Prepare environment for workflow resumption

**Begin load process now and report detailed status after completion.**