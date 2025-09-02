# Technology Investigation - Practical Example

## Example 1: Stack Selection Mode

### User Prompt
"I need to build a document collaboration platform like Google Docs but simpler, for internal company use only. 100 users max."

### LLM Execution (Prompt-as-Code Style)

```markdown
EXECUTING STACK SELECTION MODE...

## Requirement Analysis
- Scale: 100 users (small)
- Type: Document collaboration
- Deployment: Internal/corporate
- Key needs: Real-time sync, version history, access control

## Dimension 1: Frontend Framework

EVALUATING CANDIDATES:

**Minimalist Option: Vanilla JS + Web Components**
- GitHub Evidence: 
  - https://github.com/basecamp/trix (4.5k stars) - Pure JS editor
  - https://github.com/quilljs/quill (37k stars) - No framework needed
- Pros: No build step, 0 dependencies, fast load
- Cons: More boilerplate, manual state management
- Verdict: Sufficient for 100 users ✓

**Standard Option: React + Slate.js**
- GitHub Evidence:
  - https://github.com/outline/outline (20k stars) - React + Slate
  - https://github.com/facebookincubator/lexical (15k stars)
- Pros: Rich ecosystem, component reuse, familiar
- Cons: Build complexity, bundle size
- Verdict: Over-engineered for need ✗

**Selected: Vanilla JS + Web Components** (minimalist wins)

## Dimension 2: Backend Architecture

EVALUATING CANDIDATES:

**Minimalist Option: Node.js + Express + WebSockets**
- GitHub Evidence:
  - https://github.com/share/sharedb (5k stars) - OT collaboration
  - https://github.com/yjs/yjs (13k stars) - CRDT sync
- Pros: Single language, simple deployment, proven OT libs
- Cons: Manual scaling, no built-in auth
- Verdict: Perfect fit for 100 users ✓

**Standard Option: Google Apps Script**
- Constraint: Internal corporate → likely has Google Workspace
- Pros: Zero infrastructure, built-in auth, corporate IT approved
- Cons: 6-minute execution limit, quotas
- Verdict: Investigate deeper (trigger Deep Mode)

## Switching to Deep Investigation for GAS...
```

## Example 2: Deep Investigation Mode

### Triggered Investigation: Google Apps Script for Document Platform

```markdown
EXECUTING DEEP INVESTIGATION MODE FOR GOOGLE APPS SCRIPT...

## Test 1: Execution Model

TESTING CODE:
```javascript
function testGASExecution() {
  // Test 1: Cold start timing
  const start = Date.now();
  SpreadsheetApp.getActiveSpreadsheet(); // Force service load
  Logger.log(`Service load time: ${Date.now() - start}ms`);
  // Result: ~200-500ms cold start
  
  // Test 2: Parallel execution limits
  const locks = [];
  for(let i = 0; i < 10; i++) {
    locks.push(LockService.getDocumentLock());
  }
  // Result: Throws quota error at 30 locks/second
  
  // Test 3: Maximum execution time
  Utilities.sleep(300000); // 5 minutes
  // Result: Hard stops at 6 minutes for normal execution
}
```

DISCOVERED CONSTRAINTS:
- 6-minute execution limit per invocation
- 30 concurrent locks maximum  
- 200-500ms cold start penalty
- No true parallel execution (V8 isolates)

## Test 2: Real-time Collaboration Feasibility

TESTING WEBSOCKET ALTERNATIVES:
```javascript
function testRealtimeSync() {
  // Method 1: Polling with Cache Service
  CacheService.getDocumentCache().put('doc_1_ops', JSON.stringify(ops));
  // Result: 100KB cache limit, 6-hour expiration
  
  // Method 2: Google Sheets as message bus
  const sheet = SpreadsheetApp.openById('...').getSheetByName('ops');
  sheet.appendRow([timestamp, userId, operation]);
  // Result: Works but 500ms latency, rate limits at 100 writes/minute
  
  // Method 3: Firestore integration
  const firebaseUrl = 'https://project.firebaseio.com/';
  UrlFetchApp.fetch(firebaseUrl + 'docs/doc1/ops.json', {
    method: 'post',
    payload: JSON.stringify(operation)
  });
  // Result: Works well, 50ms latency, requires setup
}
```

VERDICT: GAS alone insufficient for real-time sync

## Test 3: Authentication Deep Dive

AUTHENTICATION FLOW DISCOVERED:
```javascript
function getAuthContext() {
  const user = Session.getActiveUser().getEmail();
  // Only works for Workspace domain users
  
  const token = ScriptApp.getOAuthToken();
  // Token includes these scopes:
  // - https://www.googleapis.com/auth/spreadsheets
  // - https://www.googleapis.com/auth/script.external_request
  // - https://www.googleapis.com/auth/drive
  
  const effective = Session.getEffectiveUser().getEmail();
  // Who the script runs as (deployment setting)
  
  return {
    requestUser: user,        // Empty for anonymous
    executionUser: effective, // Script owner or user
    canIdentify: user !== '', // False for public web apps
  };
}
```

KEY FINDINGS:
- Domain-restricted apps get automatic SSO
- Public apps cannot identify users without OAuth flow
- Deployment settings critical for security model

## Platform Gotchas Discovered

FROM GITHUB ISSUES ANALYSIS:
1. "Properties Service randomly loses data" 
   - Solution: Always use try-catch, implement backup to Sheet
   
2. "Script suddenly stops working after 90 days"
   - Cause: Automatic disabling of unused scripts
   - Solution: Implement heartbeat trigger
   
3. "HtmlService forms timeout after 5 minutes"
   - Solution: Implement client-side keepalive polling
   
4. "Cannot debug triggers in development"
   - Solution: Create manual test functions that simulate triggers

## Final Verdict

GOOGLE APPS SCRIPT VIABILITY:
❌ Real-time collaboration (>500ms latency)
✅ Document storage (Drive integration)
✅ Version history (Drive revisions API)  
✅ Access control (built-in sharing)
❌ 100 concurrent users (quota limits)

RECOMMENDATION:
- Return to Stack Selection
- Consider hybrid: GAS for storage + Node.js for real-time
```

## Example 3: Hybrid Mode Execution

### Combining Both Modes for Optimal Decision

```markdown
EXECUTING HYBRID MODE...

## Round 1: Quick Stack Selection (2 hours)
Candidates identified:
1. Node.js + Socket.io + MongoDB
2. Google Apps Script + Firestore
3. Deno + PostgreSQL + Server-Sent Events

## Round 2: Parallel Deep Investigation (4 hours)

WORKTREE 1: Deep investigate Node.js stack
- Discovery: Proven for real-time, complex deployment
- Key finding: Requires DevOps expertise

WORKTREE 2: Deep investigate GAS hybrid
- Discovery: Simple auth, complex real-time setup
- Key finding: Firestore adds $50/month cost

WORKTREE 3: Deep investigate Deno stack  
- Discovery: Modern, but immature ecosystem
- Key finding: Limited collaboration libraries

## Round 3: Final Selection with Deep Knowledge

SELECTED STACK:
**Google Apps Script + Firestore Hybrid**

RATIONALE WITH DEEP INSIGHTS:
- GAS handles: Auth (SSO), storage (Drive), versioning (native)
- Firestore handles: Real-time sync (50ms latency), presence
- Integration: GAS writes to Firestore, clients subscribe
- Deployment: Zero infrastructure (GAS) + managed (Firestore)
- Cost: Free (GAS) + $50/month (Firestore for 100 users)

IMPLEMENTATION BLUEPRINT:
```javascript
// GAS Backend (storage + auth)
function saveDocument(docId, content) {
  // Save to Drive
  DriveApp.getFileById(docId).setContent(content);
  
  // Push to Firestore for real-time
  UrlFetchApp.fetch(FIRESTORE_URL + `/docs/${docId}/latest.json`, {
    method: 'put',
    headers: {'Authorization': 'Bearer ' + getFirebaseToken()},
    payload: JSON.stringify({content, timestamp: Date.now()})
  });
}

// Client (real-time sync)
const unsubscribe = firebase.firestore()
  .collection('docs').doc(docId)
  .onSnapshot(doc => updateEditor(doc.data()));
```

This hybrid leverages platform strengths while avoiding weaknesses.
```

## Prompt-as-Code Meta Pattern

The above examples demonstrate the prompt-as-code pattern:

1. **Natural Language Instructions**: Each section uses prose to guide investigation
2. **Executable Decisions**: Clear branching logic (if X then investigate Y)  
3. **Evidence-Driven**: Real code tests and GitHub analysis, not assumptions
4. **Mode Transitions**: Automatic switching based on discoveries
5. **Parallel Exploration**: Worktree isolation for concurrent investigation
6. **Synthesis**: Combining findings into actionable recommendations

The LLM interprets these instructions and executes them adaptively based on project context, rather than following rigid code paths.