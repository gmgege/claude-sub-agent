---
description: "List all incomplete agent workflows with status and resume options"
allowed-tools: ["Read", "Glob", "TodoWrite"]
---

# Agent Workflow List - View Incomplete Workflows

Display all incomplete agent workflows with their current status and resume information.

## Usage

```bash
/agent-workflow-list
```

## Context

- Show all workflows in progress or paused state
- Display workflow details, progress, and resume commands
- Help users identify and continue interrupted work

## Your Role

You are the Workflow Status Reporter. Use the workflow-state-manager sub agent to load all workflow states and present a comprehensive overview of incomplete workflows.

## Implementation Steps

1. **Initialize State Manager**: Use workflow-state-manager sub agent for operations
2. **Scan Workflows**: Find all incomplete workflow files in workflow-states directory
3. **Load and Validate**: Read state details for each workflow with validation
4. **Filter and Sort**: Filter incomplete workflows and sort by relevance
5. **Format Display**: Present information in organized, readable format
6. **Provide Actions**: Show resume commands and quick actions

## State Manager Integration

```javascript
// List incomplete workflows using workflow-state-manager
const incompleteWorkflows = await useWorkflowStateManager({
  operation: 'list-states',
  filter: {
    status: ['active', 'paused', 'failed'],
    sortBy: 'lastUpdated',
    order: 'desc'
  }
});

// Format and display results
const formattedList = formatWorkflowList(incompleteWorkflows);
presentWorkflowOverview(formattedList);
```

## Expected Output Format

```
📋 Incomplete Agent Workflows

🔄 Active Workflows (2 found):

┌─────────────┬─────────────────────────────────┬─────────────────┬──────────┬─────────────────────┐
│ Workflow ID │ Feature Description             │ Current Phase   │ Progress │ Last Updated        │
├─────────────┼─────────────────────────────────┼─────────────────┼──────────┼─────────────────────┤
│ workflow_a  │ User authentication with OAuth  │ spec-developer  │ 2/5 40%  │ 2024-08-02 15:30:45 │
│ workflow_b  │ Real-time chat system          │ spec-validator  │ 4/5 80%  │ 2024-08-02 12:15:30 │
└─────────────┴─────────────────────────────────┴─────────────────┴──────────┴─────────────────────┘

📝 Workflow Details:

🆔 workflow_abc12345_1691234567890
📋 Feature: User authentication with OAuth integration
🔄 Status: paused (Session time limit reached)
🎯 Current Phase: spec-developer
📈 Progress: 2/5 phases completed (40%)
🔁 Iterations: 1/3
⏰ Last Updated: 2024-08-02 15:30:45
▶️ Resume: /agent-workflow-resume workflow_abc12345_1691234567890

🆔 workflow_def67890_1691234567891  
📋 Feature: Real-time chat system with message encryption
🔄 Status: in_progress
🎯 Current Phase: spec-validator
📈 Progress: 4/5 phases completed (80%)
🔁 Iterations: 2/3
⏰ Last Updated: 2024-08-02 12:15:30
▶️ Resume: /agent-workflow-resume workflow_def67890_1691234567891

💡 Quick Actions:
  📤 Resume most recent: /agent-workflow-resume
  🗑️ Clean completed: /agent-workflow-clean
  🆕 Start new workflow: /agent-workflow [FEATURE_DESCRIPTION]
```

---

## Execute Workflow List

Loading workflow state manager and scanning for incomplete workflows...

### 🔍 Scanning Workflow Directory

First scan the workflow directory for all saved states:
- Use workflow-state-manager sub agent to scan workflow-states directory
- Load and validate JSON state files across all status directories
- Filter for incomplete workflows (active, paused, failed statuses)
- Sort by last updated time (most recent first)

### 📊 Loading Workflow Details

Then load detailed information for each workflow:
- Read state data from each workflow file
- Extract key information (ID, feature, phase, progress)
- Calculate completion percentages
- Format timestamps for display

### 📋 Displaying Results

Finally present the organized workflow list:
- Show summary table with key information
- Display detailed breakdown for each workflow
- Provide resume commands for each workflow
- Include helpful quick action commands

**Begin workflow scan now and display results.**