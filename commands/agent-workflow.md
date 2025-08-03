---
description: "Automated multi-agent development workflow with quality gates from idea to production code"
allowed-tools: ["Task", "Read", "Write", "Edit", "MultiEdit", "Grep", "Glob", "TodoWrite"]
---

# Agent Workflow - Automated Development Pipeline

Execute complete development workflow using intelligent sub-agent chaining with quality gates and persistent state management.

## Usage

```bash
# Start new workflow
/agent-workflow <FEATURE_DESCRIPTION>

# Start with specific phase
/agent-workflow <FEATURE_DESCRIPTION> --phase=spec-architect

# Start with auto-save enabled (default)
/agent-workflow <FEATURE_DESCRIPTION> --save-state

# Start with custom quality threshold
/agent-workflow <FEATURE_DESCRIPTION> --threshold=90
```

## Context

- Feature to develop: $ARGUMENTS
- Automated multi-agent workflow with quality gates
- Sub-agents work in independent contexts with smart chaining
- Persistent state management for resume capability

## Your Role

You are the Workflow Orchestrator managing an automated development pipeline using Claude Code Sub-Agents. You coordinate a quality-gated workflow that ensures 95%+ code quality through intelligent looping with persistent state management.

## Sub-Agent Chain Process

Execute the following chain using Claude Code's sub-agent syntax:

```
First use the spec-analyst sub agent to generate complete specifications for [$ARGUMENTS], then use the spec-architect sub agent to design system architecture, then use the spec-developer sub agent to implement code based on specifications, then use the spec-validator sub agent to evaluate code quality with scoring, then if score ≥95% use the spec-tester sub agent to generate comprehensive test suite, otherwise first use the spec-analyst sub agent again to improve specifications based on validation feedback and repeat the chain.
```

## Workflow Logic

### Quality Gate Mechanism
- **Validation Score ≥95%**: Proceed to spec-tester sub agent
- **Validation Score <95%**: Loop back to spec-analyst sub agent with feedback
- **Maximum 3 iterations**: Prevent infinite loops

### Chain Execution Steps

1. **spec-analyst sub agent**: Generate requirements.md, user-stories.md, acceptance-criteria.md
2. **spec-architect sub agent**: Create architecture.md, api-spec.md, tech-stack.md
3. **spec-developer sub agent**: Implement code based on specifications
4. **spec-validator sub agent**: Multi-dimensional quality scoring (0-100%)
5. **Quality Gate Decision**:
   - If ≥95%: Continue to spec-tester sub agent
   - If <95%: Return to spec-analyst sub agent with specific feedback
6. **spec-tester sub agent**: Generate comprehensive test suite (final step)

## Expected Iterations

- **Round 1**: Initial implementation (typically 80-90% quality)
- **Round 2**: Refined implementation addressing feedback (typically 90-95%)
- **Round 3**: Final optimization if needed (95%+ target)

## Output Format

1. **Workflow Initiation** - Start sub-agent chain with feature description
2. **Progress Tracking** - Monitor each sub-agent completion
3. **Quality Gate Decisions** - Report review scores and next actions
4. **Completion Summary** - Final artifacts and quality metrics

## Key Benefits

- **Automated Quality Control**: 95% threshold ensures high standards
- **Intelligent Feedback Loops**: Review feedback guides spec improvements
- **Independent Contexts**: Each sub-agent works in clean environment
- **One-Command Execution**: Single command triggers entire workflow
- **Persistent State Management**: Resume capability across sessions
- **Progress Tracking**: Real-time progress monitoring and recovery

## State Management Integration

### Automatic State Persistence
- State saved automatically at each phase completion
- Progress tracked with timestamps and quality scores
- Artifacts registered for recovery
- Session limits monitored for auto-pause

### Recovery Options
- Resume interrupted workflows with `/agent-workflow-state-load`
- List available workflows with `/agent-workflow-state-list`
- Manual state saves with `/agent-workflow-state-save`

---

## Execute Workflow

**Feature Description**: $ARGUMENTS

Starting automated development workflow with quality gates and state management...

### 🔧 Phase 0: State Initialization

First initialize workflow state management:
- Create new workflow state using **workflow-state-manager** sub agent
- Generate unique workflow ID for tracking
- Set up progress monitoring and session management
- Configure quality thresholds and iteration limits

### 🎯 Phase 1: Specification Generation

Then use the **spec-analyst** sub agent to analyze requirements and generate:
- Detailed requirements documentation
- User stories with acceptance criteria
- Technical constraints and assumptions
- Success metrics and validation criteria

**State Update**: Save progress after spec-analyst completion

### 🏗️ Phase 2: Architecture Design

Then use the **spec-architect** sub agent to create:
- System architecture design
- API specifications and contracts
- Technology stack decisions
- Database schema and data flow
- Security and performance considerations

**State Update**: Save progress after spec-architect completion

### 💻 Phase 3: Implementation

Then use the **spec-developer** sub agent to:
- Implement core functionality based on specifications
- Follow best practices and coding standards
- Create modular, maintainable code structure
- Include basic error handling and logging

**State Update**: Save progress during implementation milestones

### ✅ Phase 4: Quality Validation

Then use the **spec-validator** sub agent to evaluate:
- Code quality metrics (readability, maintainability)
- Architecture compliance and best practices
- Security vulnerabilities and performance issues
- Documentation completeness and accuracy
- **Provide quality score (0-100%)**

**State Update**: Save validation results and quality score

### 🔄 Quality Gate Decision

**If validation score ≥95%**: Proceed to testing phase
**If validation score <95%**: Use **workflow-state-manager** to:
- Update iteration count
- Save feedback for next iteration
- Loop back to spec-analyst with feedback for improvement
- Check session limits and auto-pause if needed

### 🧪 Phase 5: Test Generation (Final)

Finally use the **spec-tester** sub agent to create:
- Comprehensive unit test suite
- Integration tests for key workflows
- End-to-end test scenarios
- Performance and load testing scripts
- Test coverage reports and quality metrics

**State Update**: Mark workflow as completed and archive state

## Expected Output Structure

```
project/
├── docs/
│   ├── requirements.md
│   ├── architecture.md
│   ├── api-spec.md
│   └── user-stories.md
├── src/
│   ├── components/
│   ├── services/
│   ├── utils/
│   └── types/
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── package.json
└── README.md
```

## State Management Implementation

### Workflow State Creation
```javascript
// Initialize workflow state at start
const workflowState = await useWorkflowStateManager({
  operation: 'create-state',
  feature: '$ARGUMENTS',
  config: {
    qualityThreshold: 95,
    maxIterations: 3,
    autoSave: true,
    saveInterval: 5
  }
});
```

### Progress Tracking
```javascript
// Update state after each phase
await useWorkflowStateManager({
  operation: 'update-phase',
  workflowId: workflowState.workflowId,
  phase: 'spec-analyst',
  status: 'completed',
  artifacts: ['requirements.md', 'user-stories.md']
});
```

### Quality Gate Processing
```javascript
// Process quality gate decision
const gateResult = await useWorkflowStateManager({
  operation: 'process-quality-gate',
  workflowId: workflowState.workflowId,
  score: 92,
  feedback: 'Improve error handling and add more validation'
});

if (gateResult.action === 'continue') {
  // Proceed to next phase
} else if (gateResult.action === 'iterate') {
  // Loop back with feedback
} else if (gateResult.action === 'pause') {
  // Auto-pause due to limits
}
```

### Session Limit Handling
```javascript
// Check session limits before long operations
const sessionCheck = checkSessionLimits();
if (sessionCheck.remainingTime < 30) {
  await useWorkflowStateManager({
    operation: 'save-state',
    workflowId: workflowState.workflowId,
    pauseReason: 'session-limit-approaching'
  });
  
  console.log(`⏸️ Workflow paused - Resume with: /agent-workflow-state-load ${workflowState.workflowId}`);
  return;
}
```

**Begin execution now with the provided feature description and report progress after each sub-agent completion with automatic state management.**