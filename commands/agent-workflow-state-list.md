---
description: "List and browse available workflow states with filtering and search capabilities"
allowed-tools: ["Task", "Read", "Write", "Edit", "Glob", "Grep", "TodoWrite"]
---

# Agent Workflow State List - Browse Available Workflows

List and browse all available workflow states with comprehensive filtering, search, and sorting capabilities.

## Usage

```bash
# List all workflows
/agent-workflow-state-list

# List by status
/agent-workflow-state-list --status=active
/agent-workflow-state-list --status=paused,completed

# Search by feature description
/agent-workflow-state-list --search="e-commerce"
/agent-workflow-state-list --pattern="*platform*"

# List recent workflows
/agent-workflow-state-list --recent=10
/agent-workflow-state-list --since="2024-08-01"

# Detailed view with artifacts
/agent-workflow-state-list --detailed
/agent-workflow-state-list --with-artifacts
```

## Context

- List workflows from persistent storage
- Apply filters and search criteria from arguments
- Display comprehensive workflow information
- Support multiple output formats

## Your Role

You are the Workflow State Browser. You will use the workflow-state-manager agent to discover, filter, and display workflow states with comprehensive information and user-friendly formatting.

## List Process

Execute the following steps to list workflow states:

### 🔍 Step 1: Discovery & Indexing

Scan and index all available workflow states:

```javascript
// Scan all state directories
const stateDirectories = ['active', 'paused', 'completed', 'failed', 'archived'];
const allWorkflows = [];

for (const directory of stateDirectories) {
  const workflows = scanDirectory(`./workflow-states/${directory}`);
  allWorkflows.push(...workflows.map(w => ({...w, status: directory})));
}

// Load and validate each state
const validWorkflows = [];
for (const workflow of allWorkflows) {
  try {
    const state = loadAndValidateState(workflow.id);
    validWorkflows.push(state);
  } catch (error) {
    console.warn(`Skipping invalid workflow: ${workflow.id} - ${error.message}`);
  }
}
```

### 🔍 Step 2: Filtering & Search

Apply user-specified filters and search criteria:

```javascript
// Parse filter arguments
const filters = parseFilterArguments('$ARGUMENTS');

// Apply status filter
if (filters.status) {
  workflows = workflows.filter(w => filters.status.includes(w.status));
}

// Apply search/pattern filter
if (filters.search || filters.pattern) {
  workflows = workflows.filter(w => 
    matchesSearchCriteria(w, filters.search, filters.pattern)
  );
}

// Apply date filters
if (filters.since || filters.until) {
  workflows = workflows.filter(w => 
    isWithinDateRange(w.lastUpdated, filters.since, filters.until)
  );
}

// Apply limit filter
if (filters.recent || filters.limit) {
  workflows = workflows
    .sort((a, b) => new Date(b.lastUpdated) - new Date(a.lastUpdated))
    .slice(0, filters.recent || filters.limit);
}
```

### 📊 Step 3: Information Gathering

Gather detailed information for display:

```javascript
// Collect summary statistics
const statistics = calculateStatistics(workflows);

// Enrich with additional metadata
const enrichedWorkflows = workflows.map(workflow => ({
  ...workflow,
  displayInfo: {
    duration: calculateWorkflowDuration(workflow),
    estimatedCompletion: estimateCompletion(workflow),
    artifactCount: countArtifacts(workflow),
    lastActivity: formatRelativeTime(workflow.lastUpdated),
    riskLevel: assessRiskLevel(workflow)
  }
}));

// Sort by relevance or user preference
const sortBy = filters.sort || 'lastUpdated';
enrichedWorkflows.sort(createSortComparator(sortBy));
```

### 📋 Step 4: Display Formatting

Format and display the workflow information:

```javascript
// Generate appropriate display format
const displayFormat = filters.detailed ? 'detailed' : 'summary';
const output = formatWorkflowList(enrichedWorkflows, displayFormat);

// Add statistics and summary
const summary = generateListSummary(statistics, filters);

// Combine and present
presentWorkflowList(summary, output);
```

## Filter Options

### Status Filters
- `--status=active`: Show only active workflows
- `--status=paused`: Show only paused workflows  
- `--status=completed`: Show only completed workflows
- `--status=failed`: Show only failed workflows
- `--status=archived`: Show only archived workflows
- `--status=active,paused`: Multiple statuses (comma-separated)

### Search Filters
- `--search="text"`: Full-text search in feature descriptions
- `--pattern="*text*"`: Glob pattern matching
- `--feature="exact"`: Exact feature name matching
- `--id="pattern"`: Search by workflow ID pattern

### Date Filters
- `--since="2024-08-01"`: Since specific date
- `--until="2024-08-31"`: Until specific date
- `--recent=10`: Most recent N workflows
- `--today`: Workflows updated today
- `--week`: Workflows from past week

### Display Options
- `--detailed`: Show detailed information
- `--summary`: Show summary only (default)
- `--with-artifacts`: Include artifact information
- `--with-progress`: Include progress details
- `--json`: Output in JSON format

### Sorting Options
- `--sort=updated`: Sort by last updated (default)
- `--sort=created`: Sort by creation date
- `--sort=progress`: Sort by completion percentage
- `--sort=name`: Sort alphabetically by feature name

## Display Formats

### Summary Format (Default)
```
📋 Workflow States Overview

📊 Summary: 12 workflows found
  🟢 Active: 3
  ⏸️ Paused: 2  
  ✅ Completed: 5
  ❌ Failed: 1
  📦 Archived: 1

🔄 Active Workflows:
  📝 workflow_ecommerce_platform_1691234567890
     Feature: E-commerce platform with payment integration
     Progress: ████████▒▒ 80% (4/5 phases)
     Phase: spec-validator
     Updated: 2 hours ago

  📝 workflow_dev_platform_1691234567890  
     Feature: 个人开发者需求调研分析平台
     Progress: ████▒▒▒▒▒▒ 40% (2/5 phases)
     Phase: spec-developer
     Updated: 30 minutes ago

  📝 workflow_mobile_app_1691234567890
     Feature: Cross-platform mobile productivity app
     Progress: ██▒▒▒▒▒▒▒▒ 20% (1/5 phases)  
     Phase: spec-architect
     Updated: 1 hour ago
```

### Detailed Format
```
📋 Detailed Workflow Information

🔄 Active Workflows (3 found):

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📝 workflow_ecommerce_platform_1691234567890
   🎯 Feature: E-commerce platform with payment integration
   📅 Created: 2024-08-02 10:15:30 UTC
   🕐 Updated: 2024-08-03 13:22:15 UTC (2 hours ago)
   ⏱️ Duration: 1d 3h 7m
   
   📊 Progress: 80% complete (4/5 phases)
   🎯 Current Phase: spec-validator (in progress)
   🔁 Iteration: 1/3
   ⭐ Last Score: 92%
   
   📁 Artifacts (15 files, 156.2KB):
     📄 requirements.md, user-stories.md, acceptance-criteria.md
     📄 architecture.md, api-spec.md, tech-stack.md
     📂 src/ (45 files), tests/ (8 files)
     📄 validation-report.md
   
   📊 Phase Status:
     ✅ spec-analyst: Completed (45m)
     ✅ spec-architect: Completed (1h 20m) - Score: 96%
     ✅ spec-developer: Completed (18h 15m) - Score: 94%
     🔄 spec-validator: In Progress (2h 7m)
     ⏳ spec-tester: Pending
   
   ⚡ Next Action: Complete validation review
   🎯 ETA: ~3h remaining

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

📝 workflow_dev_platform_1691234567890
   🎯 Feature: 个人开发者需求调研分析平台
   📅 Created: 2024-08-02 14:30:45 UTC
   🕐 Updated: 2024-08-03 15:00:12 UTC (30 minutes ago)
   ⏱️ Duration: 1d 30m
   
   📊 Progress: 40% complete (2/5 phases)
   🎯 Current Phase: spec-developer (60% complete)
   🔁 Iteration: 1/3
   
   📁 Artifacts (8 files, 61.1KB):
     📄 requirements.md, user-stories.md
     📄 architecture.md, api-spec.md
     📂 src/ (12 files)
   
   📊 Phase Status:
     ✅ spec-analyst: Completed (45m)
     ✅ spec-architect: Completed (1h 10m) - Score: 95%
     🔄 spec-developer: In Progress (22h 35m)
     ⏳ spec-validator: Pending
     ⏳ spec-tester: Pending
   
   ⚡ Next Action: Complete user authentication module
   🎯 ETA: ~8h remaining
```

### JSON Format
```json
{
  "summary": {
    "total": 12,
    "byStatus": {
      "active": 3,
      "paused": 2,
      "completed": 5,
      "failed": 1,
      "archived": 1
    }
  },
  "workflows": [
    {
      "workflowId": "workflow_ecommerce_platform_1691234567890",
      "feature": "E-commerce platform with payment integration",
      "status": "active",
      "currentPhase": "spec-validator", 
      "progress": {
        "completed": 4,
        "total": 5,
        "percentage": 80
      },
      "createdAt": "2024-08-02T10:15:30.000Z",
      "lastUpdated": "2024-08-03T13:22:15.000Z",
      "artifacts": {
        "count": 15,
        "totalSize": 159948
      }
    }
  ]
}
```

## Error Handling

Handle various listing scenarios:

### No Workflows Found
```
📋 No workflows found matching criteria

🔍 Applied Filters:
  📊 Status: active
  🔍 Search: "mobile app"
  📅 Since: 2024-08-01

💡 Suggestions:
  📋 /agent-workflow-state-list (show all)
  🔍 /agent-workflow-state-list --search="app"
  📅 /agent-workflow-state-list --since="2024-07-01"
```

### Directory Access Issues
```
⚠️ Some directories are inaccessible:
  ❌ ./workflow-states/archived/ (permission denied)
  ✅ ./workflow-states/active/ (3 workflows)
  ✅ ./workflow-states/completed/ (5 workflows)

📊 Showing 8/12 total workflows
💡 Check directory permissions for complete listing
```

### Corrupted States
```
⚠️ Found 2 corrupted workflow states:
  ❌ workflow_test_123.json (invalid JSON)
  ❌ workflow_old_456.json (missing required fields)

📊 Showing 10/12 valid workflows
🧹 Run /agent-workflow-state-clean to remove corrupted states
```

## Implementation Steps

1. **Parse Arguments**: Extract filter criteria and display options
2. **Scan Directories**: Search all workflow state directories
3. **Load States**: Load and validate each workflow state file
4. **Apply Filters**: Filter workflows based on user criteria
5. **Enrich Data**: Calculate additional metadata and display info
6. **Sort Results**: Order workflows by specified criteria
7. **Format Output**: Generate appropriate display format
8. **Present Results**: Display formatted workflow list with summary

## Expected Output

```
📋 Workflow States Overview

🔍 Filter: status=active,paused | recent=10

📊 Summary: 5 workflows found (from 12 total)
  🟢 Active: 3
  ⏸️ Paused: 2

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

🟢 Active Workflows:

📝 workflow_ecommerce_platform_1691234567890
   🎯 E-commerce platform with payment integration  
   📊 Progress: ████████▒▒ 80% (spec-validator)
   🕐 Updated: 2 hours ago | ⏱️ Duration: 1d 3h

📝 workflow_dev_platform_1691234567890
   🎯 个人开发者需求调研分析平台
   📊 Progress: ████▒▒▒▒▒▒ 40% (spec-developer)  
   🕐 Updated: 30 minutes ago | ⏱️ Duration: 1d

📝 workflow_mobile_app_1691234567890
   🎯 Cross-platform mobile productivity app
   📊 Progress: ██▒▒▒▒▒▒▒▒ 20% (spec-architect)
   🕐 Updated: 1 hour ago | ⏱️ Duration: 3h

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

⏸️ Paused Workflows:

📝 workflow_api_service_1691234567890
   🎯 RESTful API service with microservices
   📊 Progress: ██████▒▒▒▒ 60% (spec-developer)
   🕐 Paused: 2 days ago | ⏱️ Duration: 5h

📝 workflow_dashboard_1691234567890  
   🎯 Analytics dashboard with real-time data
   📊 Progress: ██▒▒▒▒▒▒▒▒ 20% (spec-architect)
   🕐 Paused: 1 week ago | ⏱️ Duration: 2h

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

💡 Actions:
  🔄 Resume: /agent-workflow-state-load <WORKFLOW_ID>
  👁️ Details: /agent-workflow-state-list --detailed
  🧹 Cleanup: /agent-workflow-state-clean
```

---

## Execute List

**Filter Criteria**: $ARGUMENTS

Initiating workflow state listing using the workflow-state-manager agent...

### 🔍 Phase 1: Discovery & Scanning

First discover all available workflow states:
- Scan ./workflow-states directory and subdirectories
- Load and validate each workflow state file
- Build comprehensive workflow inventory
- Handle any corrupted or inaccessible states

### 📊 Phase 2: Filtering & Processing

Then apply filters and enrich the data:
- Parse user filter criteria from arguments
- Apply status, search, date, and other filters
- Calculate additional metadata and statistics
- Sort results by specified criteria

### 📋 Phase 3: Formatting & Display

Finally format and present the results:
- Generate appropriate display format (summary/detailed/JSON)
- Create comprehensive workflow listing
- Include summary statistics and next actions
- Present user-friendly formatted output

**Begin listing process now and display workflows matching the criteria.**