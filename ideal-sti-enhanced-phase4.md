# Enhanced Phase 4: Dual-Mode Technology Investigation

## Integration with IDEAL-STI System

This enhanced Phase 4 replaces the existing technology research phase with a dual-mode approach that distinguishes between Stack Selection and Deep Investigation.

### Phase 4: Technology Investigation (Dual Mode)

```markdown
PHASE 4 INITIALIZATION:

1. **Mode Determination**
   Analyze project requirements from Phase 1-3:
   - If greenfield project → Start with STACK SELECTION
   - If migration project → Start with DEEP INVESTIGATION of current stack
   - If platform-constrained → Start with DEEP INVESTIGATION of platform
   - If technology-agnostic → Start with STACK SELECTION

2. **Execution Branch**
```

## Branch A: Stack Selection Mode

```markdown
STACK SELECTION EXECUTION:

### Step 1: Dimension Mapping
From Phase 3 requirements, identify selection dimensions:
- User scale → Influences database and infrastructure
- Real-time needs → Influences backend and protocol choices
- Team size → Influences complexity tolerance
- Budget constraints → Influences managed vs self-hosted

### Step 2: Candidate Generation
For each dimension, generate 3 candidates:
MINIMALIST: What's the absolute simplest solution?
STANDARD: What do most teams choose?
ADVANCED: What would scale to 10x requirements?

### Step 3: Parallel Research Worktrees
Create isolated research worktrees for each dimension:

FRONTEND_RESEARCH:
- Find 5+ GitHub repos for each framework candidate
- Extract bundle sizes, performance metrics, learning curves
- Document ecosystem health (npm downloads, release frequency)

BACKEND_RESEARCH:
- Find 5+ production APIs using each runtime
- Extract scaling patterns, deployment complexities
- Document operational requirements (monitoring, debugging)

DATABASE_RESEARCH:
- Find 5+ apps with similar data patterns
- Extract query performance, scaling stories
- Document migration paths and pain points

### Step 4: Synthesis Matrix
| Dimension | Minimalist | Standard | Advanced | Recommendation |
|-----------|------------|----------|----------|----------------|
| Frontend  | Vanilla JS | React    | Next.js  | [Based on reqs]|
| Backend   | Express    | NestJS   | K8s+gRPC | [Based on reqs]|
| Database  | SQLite     | Postgres | Mongo+Redis | [Based on reqs]|

### Step 5: Compatibility Scoring
Test combinations for impedance mismatches:
- Language boundaries (serialization overhead)
- Deployment complexity (can they co-locate?)
- Operational burden (how many things to monitor?)
- Team cognitive load (how many paradigms?)

### Step 6: Output
SELECTED STACK:
- Primary: [Most compatible combination]
- Rationale: [Why this optimizes for requirements]
- Upgrade path: [How to scale if needed]
- Downgrade path: [How to simplify if over-built]
```

## Branch B: Deep Investigation Mode

```markdown
DEEP INVESTIGATION EXECUTION:

### Step 1: Platform Archaeology
Select target platform from Phase 3 or Stack Selection:

REPOSITORY ANALYSIS:
- Clone 5+ mature production repos using this stack
- Generate file structure patterns
- Extract initialization sequences
- Document configuration patterns

### Step 2: Execution Model Research
Create platform-specific test harness:

TEST_EXECUTION_MODEL:
```javascript
// For Google Apps Script example
function testExecutionModel() {
  Logger.log("Cold start: " + new Date());
  PropertiesService.getScriptProperties(); // Force service load
  Logger.log("Service loaded: " + new Date());
  
  // Test parallel execution
  const urls = Array(10).fill('https://api.github.com/zen');
  const responses = UrlFetchApp.fetchAll(urls);
  Logger.log("Parallel complete: " + new Date());
  
  // Test execution limits
  const startTime = Date.now();
  while(Date.now() - startTime < 60000) {
    // Test 1-minute execution
  }
}
```

### Step 3: State Management Investigation
Map state boundaries and persistence:

STATE_LAYERS:
- Request state (dies after response)
- Session state (where stored, how long)
- Application state (global, shared)
- Persistent state (database, files)

For each layer, document:
- Read/write patterns
- Concurrency behavior
- Failure modes
- Recovery patterns

### Step 4: Authentication Deep Dive
Trace complete auth flow:

1. Initial authentication (where credentials go)
2. Token generation (what claims included)
3. Token storage (client vs server side)
4. Token refresh (automatic vs manual)
5. Permission evaluation (when checked)
6. Audit logging (what's recorded)

### Step 5: UI-Server Communication
Map the complete data flow:

REQUEST_FLOW:
Client → [Serialization] → Network → [Deserialization] → Server
                ↓                            ↓
           [Validation]                [Processing]
                ↓                            ↓
           [Security]                  [Business Logic]
                ↓                            ↓
Response ← [Serialization] ← Network ← [Response Format]

For each step, document:
- Data transformations required
- Size/complexity limits
- Error handling patterns
- Performance characteristics

### Step 6: Platform Gotchas
Extract from GitHub issues and Stack Overflow:

COMMON_FAILURES:
- "This works locally but fails in production because..."
- "The documentation doesn't mention that..."
- "You must always... or it silently fails"
- "Never do... even though it seems logical"

### Step 7: Output
IMPLEMENTATION GUIDE:
- Complete setup checklist
- Boilerplate with explanations
- Pattern library (copy-paste ready)
- Anti-pattern warnings
- Performance optimization points
- Testing strategies specific to platform
- Deployment pipeline requirements
```

## Mode Transition Logic

```markdown
AUTOMATIC MODE TRANSITIONS:

Selection → Investigation:
TRIGGER: Stack selected with confidence > 80%
ACTION: Automatically begin deep investigation of selected stack
OUTPUT: Implementation-ready knowledge

Investigation → Selection:
TRIGGER: Deep investigation reveals blocking issues
ACTION: Return to selection with new constraints
OUTPUT: Alternative stack options

PARALLEL EXECUTION:
When time permits (IDEAL_STI_MODE=thorough):
1. Run Stack Selection to identify top 3 candidates
2. Spawn parallel worktrees for deep investigation of each
3. Synthesize findings for final selection
4. Deep investigate winner for implementation details
```

## Integration with Other Phases

```markdown
PHASE DEPENDENCIES:

FROM Phase 3 (Feasibility):
- Technical constraints → Guide stack selection
- Resource limits → Eliminate complex options
- Compliance requirements → Filter viable stacks

TO Phase 5 (Requirements):
- Stack capabilities → Shape functional requirements
- Platform limits → Define non-functional requirements
- Implementation patterns → Inform acceptance criteria

TO Phase 7 (Architecture):
- Deep investigation → Detailed component design
- Platform patterns → Architecture decisions
- State management → Data flow design

TO Phase 8 (Decisions):
- Trade-offs discovered → Document decisions
- Workarounds needed → Record rationale
- Platform gotchas → Warning documentation
```

## Validation Gates

```markdown
STACK SELECTION VALIDATION:
✓ At least 3 candidates evaluated per dimension
✓ 5+ GitHub repos analyzed per candidate
✓ Compatibility matrix completed
✓ Clear recommendation with rationale

DEEP INVESTIGATION VALIDATION:
✓ Execution model documented with examples
✓ State management patterns identified
✓ Authentication flow mapped
✓ Platform gotchas documented
✓ Boilerplate code provided
```