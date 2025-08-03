---
description: "Resume interrupted agent workflow from saved state"
allowed-tools: ["Task", "Read", "Write", "Edit", "MultiEdit", "Grep", "Glob", "TodoWrite"]
---

# Agent Workflow Resume - Continue Development Pipeline

Resume an interrupted agent workflow from saved state with full context restoration.

## Usage

```bash
/agent-workflow-resume <WORKFLOW_ID>
```

Or resume the most recent incomplete workflow:
```bash
/agent-workflow-resume
```

## Context

- Workflow ID: $ARGUMENTS (or auto-detect most recent)
- Resume interrupted development pipeline
- Restore context and continue from last checkpoint

## Your Role

You are the Workflow Orchestrator resuming an automated development pipeline. You will restore the workflow state, analyze progress, and continue from the last completed phase.

## Resume Process

Execute the following steps to resume workflow:

### 🔍 Step 1: Load Workflow State

Load the workflow state manager and restore saved progress:

```javascript
const WorkflowStateManager = require('./lib/workflow-state');
const WorkflowCheckpointManager = require('./lib/workflow-checkpoints');

const stateManager = new WorkflowStateManager();
const checkpointManager = new WorkflowCheckpointManager();

// Load specific workflow or find most recent
const workflowId = '$ARGUMENTS' || findMostRecentWorkflow();
const state = stateManager.loadState(workflowId);
```

### 📊 Step 2: Display Resume Information

Show current workflow status and progress:

```
🔄 Resuming Workflow: [WORKFLOW_ID]
📝 Feature: [FEATURE_DESCRIPTION]
🎯 Current Phase: [CURRENT_PHASE]
📈 Progress: [X/5] phases completed
🔁 Iterations: [X/3]
```

### 🚀 Step 3: Continue Workflow Execution

Resume from the current phase using the appropriate sub-agent:

- **If current phase is spec-analyst**: Continue requirements analysis
- **If current phase is spec-architect**: Continue architecture design  
- **If current phase is spec-developer**: Continue implementation
- **If current phase is spec-validator**: Continue quality validation
- **If current phase is spec-tester**: Continue test generation

### 🎯 Step 4: Quality Gate Management

For validation phases, check previous scores and feedback:
- **Score ≥95%**: Proceed to next phase
- **Score <95%**: Continue improvement loop
- **Max iterations reached**: Proceed with warnings

### ⚠️ Step 5: Session Limit Awareness

Monitor session time and pause if approaching limits:
- **>30 min remaining**: Continue normally
- **<30 min remaining**: Show warning, offer pause option
- **<5 min remaining**: Auto-pause and save state

## Error Handling

Handle common resume scenarios:

### Workflow Not Found
```
❌ Workflow not found: [WORKFLOW_ID]
💡 List available workflows: /agent-workflow-list
```

### Corrupted State
```
❌ Workflow state corrupted: [WORKFLOW_ID]
🔧 Attempting automatic recovery...
💡 If issues persist, start new workflow: /agent-workflow [FEATURE]
```

### Session Limits
```
⏸️ Session limit approaching - auto-pausing workflow
🔄 Resume later with: /agent-workflow-resume [WORKFLOW_ID]
⏰ Estimated time remaining: [X] minutes
```

## Implementation Steps

1. **Validate Arguments**: Check if workflow ID provided or detect most recent
2. **Load State**: Restore workflow state from JSON persistence
3. **Validate State**: Ensure state integrity and compatibility
4. **Display Status**: Show current progress and next steps
5. **Resume Execution**: Continue from current phase using appropriate sub-agent
6. **Monitor Limits**: Track session time and handle limits gracefully
7. **Save Progress**: Continuously checkpoint progress

## Expected Output

```
🔄 Resuming Agent Workflow Pipeline

📋 Workflow Details:
  🆔 ID: workflow_abc12345_1691234567890
  📝 Feature: User authentication with OAuth integration  
  🎯 Current Phase: spec-developer
  📈 Progress: 2/5 phases completed (40%)
  🔁 Iterations: 1/3
  ⏰ Last Updated: 2024-08-02 15:30:45

📊 Phase Status:
  ✅ spec-analyst: Completed (Score: N/A)
  ✅ spec-architect: Completed (Score: N/A)
  🔄 spec-developer: In Progress
  ⏳ spec-validator: Pending
  ⏳ spec-tester: Pending

🚀 Resuming from spec-developer phase...
⏰ Session Time Remaining: 3h 15m

Continuing with implementation based on architectural specifications...
```

---

## Execute Resume

**Workflow ID**: $ARGUMENTS

Loading workflow state manager and resuming development pipeline...

### 🔍 Phase 1: State Recovery

First load the workflow state and validate integrity:
- Load workflow state from persistent storage
- Validate state structure and compatibility
- Check for any corruption or missing data
- Display current progress and status

### 📊 Phase 2: Context Restoration

Then restore the development context:
- Load all previously generated artifacts
- Review completed phase outputs
- Understand current requirements and architecture
- Prepare context for next phase execution

### 🚀 Phase 3: Workflow Continuation  

Finally continue the workflow execution:
- Start from current phase checkpoint
- Execute appropriate sub-agent for current phase
- Monitor session limits and pause if needed
- Save progress at each completion milestone

**Begin resume process now and report status after state loading.**