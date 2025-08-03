---
description: "Clean up old, corrupted, or unnecessary workflow states with safety checks"
allowed-tools: ["Task", "Read", "Write", "Edit", "Glob", "Grep", "TodoWrite"]
---

# Agent Workflow State Clean - Maintain Storage Health

Clean up old, corrupted, or unnecessary workflow states while maintaining data integrity and providing comprehensive safety checks.

## Usage

```bash
# Interactive cleanup with prompts
/agent-workflow-state-clean

# Clean completed workflows older than 30 days
/agent-workflow-state-clean --completed --older-than=30d

# Clean failed workflows older than 7 days
/agent-workflow-state-clean --failed --older-than=7d

# Remove corrupted states with repair attempt
/agent-workflow-state-clean --corrupted --repair

# Dry run (show what would be cleaned)
/agent-workflow-state-clean --dry-run

# Force cleanup without prompts
/agent-workflow-state-clean --force --older-than=60d
```

## Context

- Clean workflow state storage
- Apply safety filters and age limits
- Maintain data integrity and backups
- Report cleanup results and space saved

## Your Role

You are the Workflow State Maintenance Specialist. You will use the workflow-state-manager agent to safely clean up workflow states while preserving important data and maintaining storage health.

## Cleanup Process

Execute the following steps to clean workflow states:

### 🔍 Step 1: Analysis & Discovery

Analyze storage and identify cleanup candidates:

```javascript
// Scan all workflow directories
const allStates = scanAllWorkflowDirectories();

// Categorize by cleanup criteria
const cleanupCandidates = {
  corrupted: [],
  old_completed: [],
  old_failed: [],
  old_archived: [],
  large_temp: [],
  orphaned: []
};

// Analyze each state
for (const state of allStates) {
  const analysis = analyzeStateForCleanup(state);
  
  if (analysis.isCorrupted) {
    cleanupCandidates.corrupted.push({...state, reason: analysis.corruptionReason});
  }
  
  if (analysis.isOld && state.status === 'completed') {
    cleanupCandidates.old_completed.push({...state, age: analysis.age});
  }
  
  if (analysis.isOld && state.status === 'failed') {
    cleanupCandidates.old_failed.push({...state, age: analysis.age});
  }
}
```

### 📊 Step 2: Safety Assessment

Assess safety and apply protection rules:

```javascript
// Apply safety filters
const protectionRules = {
  minAge: {
    completed: '7d',    // Don't delete completed workflows < 7 days
    failed: '3d',       // Don't delete failed workflows < 3 days
    active: 'never',    // Never auto-delete active workflows
    paused: 'never'     // Never auto-delete paused workflows
  },
  maxBatch: 50,         // Max workflows to clean in one operation
  requireBackup: true,  // Require backup before deletion
  confirmationThreshold: 10 // Require confirmation for >10 items
};

// Filter candidates by safety rules
const safeCandidates = applySafetyFilters(cleanupCandidates, protectionRules);

// Calculate cleanup impact
const impact = calculateCleanupImpact(safeCandidates);
```

### 🗑️ Step 3: Cleanup Execution

Execute the cleanup with proper safeguards:

```javascript
// Create backup if needed
const backupInfo = {};
if (options.backup !== false) {
  backupInfo = createCleanupBackup(safeCandidates);
}

// Execute cleanup by category
const results = {
  deleted: [],
  repaired: [],
  failed: [],
  skipped: []
};

// Process corrupted states
for (const state of safeCandidates.corrupted) {
  try {
    if (options.repair && canRepair(state)) {
      const repaired = repairCorruptedState(state);
      results.repaired.push(repaired);
    } else {
      deleteState(state);
      results.deleted.push(state);
    }
  } catch (error) {
    results.failed.push({...state, error: error.message});
  }
}

// Process old states
for (const category of ['old_completed', 'old_failed', 'old_archived']) {
  for (const state of safeCandidates[category]) {
    try {
      archiveOrDeleteState(state, options);
      results.deleted.push(state);
    } catch (error) {
      results.failed.push({...state, error: error.message});
    }
  }
}
```

### ✅ Step 4: Verification & Reporting

Verify cleanup success and generate reports:

```javascript
// Verify deletions and updates
const verification = verifyCleanupResults(results);

// Update indexes
rebuildWorkflowIndexes();

// Calculate space saved
const spaceSaved = calculateSpaceSaved(results.deleted);

// Generate comprehensive report
const report = generateCleanupReport({
  results,
  spaceSaved,
  backupInfo,
  verification
});
```

## Cleanup Options

### Target Selection
- `--completed`: Clean completed workflows
- `--failed`: Clean failed workflows  
- `--archived`: Clean archived workflows
- `--corrupted`: Clean corrupted states
- `--temp`: Clean temporary files
- `--all`: Clean all eligible states

### Age Filters
- `--older-than=30d`: Older than 30 days
- `--older-than=1w`: Older than 1 week
- `--older-than=6m`: Older than 6 months
- `--newer-than=7d`: Newer than 7 days (for testing)

### Safety Options
- `--dry-run`: Show what would be cleaned without doing it
- `--force`: Skip confirmation prompts
- `--no-backup`: Skip backup creation
- `--repair`: Attempt to repair corrupted states

### Scope Options
- `--limit=50`: Limit number of states to clean
- `--size-limit=100MB`: Only clean if total size exceeds limit
- `--interactive`: Prompt for each state individually

## Safety Features

### Protection Rules
```
🛡️ Built-in Safety Protections:

1. Active Workflow Protection:
   ✅ Never auto-delete active workflows
   ✅ Never auto-delete paused workflows
   ⚠️ Require explicit confirmation for recent states

2. Age Thresholds:
   📅 Completed: Minimum 7 days old
   📅 Failed: Minimum 3 days old  
   📅 Archived: Minimum 30 days old

3. Batch Limits:
   🔢 Maximum 50 workflows per cleanup
   🔢 Maximum 500MB data per cleanup
   ⏰ Rate limiting to prevent system overload

4. Backup Requirements:
   💾 Automatic backup before deletion
   🔍 Verification of backup integrity
   📁 Backup retention for 30 days
```

### Confirmation Prompts
```
⚠️ Cleanup Confirmation Required

🗑️ About to clean:
  📋 12 completed workflows (older than 30 days)
  ❌ 3 failed workflows (older than 7 days)
  🗂️ 5 archived workflows (older than 6 months)

💾 Space to reclaim: 145.7 MB

🛡️ Safety measures:
  ✅ Backup will be created
  ✅ All states are older than safety thresholds
  ✅ No active or recent workflows affected

❓ Proceed with cleanup? [y/N]: 
```

## Cleanup Categories

### 1. Corrupted States
```javascript
// Detect corruption patterns
const corruptionChecks = {
  invalidJson: checkJsonValidity,
  missingFields: checkRequiredFields,
  brokenReferences: checkArtifactReferences,
  schemaViolations: validateSchema,
  orphanedFiles: checkOrphanedArtifacts
};

// Repair strategies
const repairStrategies = {
  invalidJson: attemptJsonRecovery,
  missingFields: populateDefaultFields,
  brokenReferences: repairReferences,
  schemaViolations: migrateSchema
};
```

### 2. Old Workflows
```javascript
// Age-based cleanup rules
const ageRules = {
  completed: {
    warning: '30d',   // Warn if cleaning completed < 30 days
    safe: '60d',      // Safe to clean completed > 60 days
    archive: '180d'   // Archive instead of delete > 180 days
  },
  failed: {
    warning: '7d',
    safe: '30d',
    archive: '90d'
  }
};
```

### 3. Large Files
```javascript
// Size-based cleanup
const sizeThresholds = {
  maxStateSize: '10MB',     // Warn for states > 10MB
  maxArtifactSize: '50MB',  // Clean large artifacts
  totalSizeLimit: '1GB'    // Archive if total > 1GB
};
```

## Error Handling

Handle various cleanup scenarios:

### Permission Issues
```
❌ Permission denied: ./workflow-states/archived/
🔧 Some files could not be cleaned due to permissions
📊 Cleaned: 8/12 target files
💡 Check file permissions and run as appropriate user
```

### Disk Space Issues
```
⚠️ Low disk space detected
💿 Available: 850MB, Cleanup will free: 145MB
✅ Proceeding with cleanup to free space
💡 Consider archiving large artifacts to external storage
```

### Active Workflow Conflicts
```
⚠️ Found active workflows in cleanup targets:
  🔄 workflow_ecommerce_platform_1691234567890 (last activity: 5 minutes ago)
  🔄 workflow_api_service_1691234567890 (last activity: 2 hours ago)

🛡️ These workflows will be skipped for safety
📊 Continuing with 8/10 target workflows
```

### Backup Failures
```
❌ Backup creation failed: insufficient space
🛡️ Cleanup aborted for safety
💿 Need 200MB free space for backup
🧹 Clean temporary files first or use --no-backup (not recommended)
```

## Implementation Steps

1. **Parse Arguments**: Extract cleanup criteria and safety options
2. **Scan States**: Discover all workflow states across directories
3. **Analyze Candidates**: Categorize states by cleanup criteria
4. **Apply Safety Filters**: Remove protected states and apply age limits
5. **Calculate Impact**: Determine space savings and affected workflows
6. **Create Backup**: Backup states before deletion if required
7. **Execute Cleanup**: Delete or repair states by category
8. **Verify Results**: Confirm successful cleanup and update indexes
9. **Generate Report**: Provide detailed cleanup summary and statistics

## Expected Output

```
🧹 Workflow State Cleanup

🔍 Analysis Phase:
  📂 Scanned: 45 total workflow states
  🎯 Candidates: 20 states eligible for cleanup
  🛡️ Protected: 25 states (active, recent, or paused)

📊 Cleanup Targets:
  ✅ Completed (>30d): 12 workflows (89.4 MB)
  ❌ Failed (>7d): 3 workflows (12.1 MB)
  🗂️ Archived (>6m): 5 workflows (44.2 MB)
  💥 Corrupted: 2 states (1.1 MB)

💾 Total Space to Reclaim: 146.8 MB

🛡️ Safety Checks:
  ✅ All targets older than safety thresholds
  ✅ No active or paused workflows affected  
  ✅ Backup space available (200 MB)
  ✅ Cleanup size within limits

💾 Creating backup...
  📁 Backup location: ./workflow-states/backups/cleanup_2024-08-03_15-30-45/
  💿 Backup size: 146.8 MB
  ✅ Backup created successfully

🗑️ Executing cleanup...
  ✅ Deleted: workflow_old_project_1690000000000.json (15.2 MB)
  ✅ Deleted: workflow_failed_test_1689000000000.json (3.4 MB)
  ✅ Repaired: workflow_corrupted_1691000000000.json (recovered)
  ⚠️ Skipped: workflow_recent_1691200000000.json (too recent)
  
📊 Cleanup Results:
  🗑️ Deleted: 17 workflows
  🔧 Repaired: 2 workflows  
  ⏭️ Skipped: 1 workflow
  ❌ Failed: 0 workflows

💾 Space Reclaimed: 143.4 MB
📈 Storage Health: Excellent (15% utilization)

✅ Cleanup completed successfully!

🧹 Maintenance Recommendations:
  📅 Next cleanup: Recommended in 30 days
  📦 Archive suggestion: 3 completed workflows ready for archival
  🔍 Monitor: workflow_large_project_* (approaching size limits)
```

---

## Execute Cleanup

**Cleanup Criteria**: $ARGUMENTS

Initiating workflow state cleanup using the workflow-state-manager agent...

### 🔍 Phase 1: Analysis & Discovery

First analyze storage and identify cleanup candidates:
- Scan all workflow state directories and files
- Categorize states by status, age, and condition
- Identify corrupted, old, or unnecessary states
- Calculate potential space savings and impact

### 🛡️ Phase 2: Safety Assessment

Then apply safety filters and protection rules:
- Check age thresholds and protection rules
- Filter out active, recent, or important workflows
- Verify backup requirements and disk space
- Prepare safe cleanup plan with confirmations

### 🗑️ Phase 3: Cleanup Execution

Finally execute the cleanup with proper safeguards:
- Create backup of states to be cleaned
- Process cleanup by category (corrupted, old, etc.)
- Attempt repairs where possible
- Update indexes and verify results

**Begin cleanup process now and report detailed results after completion.**