# Tech-Investigator Agent - Real-World Examples

## Example 1: Google Apps Script Deep Investigation

### Invocation
```bash
/prompt tech-investigator --mode deep --stack "google-apps-script" --focus "execution-model state-management auth-flow"
```

### Agent Execution (Prompt-as-Code)

```markdown
EXECUTING TECH-INVESTIGATOR: Deep Mode
Stack: google-apps-script
Focus: execution-model, state-management, auth-flow

## Investigation 1: Execution Model

CREATING TEST HARNESS...

```javascript
// Test 1: Execution Limits Discovery
function investigateExecutionModel() {
  const tests = {
    coldStart: () => {
      const start = Date.now();
      SpreadsheetApp.getActiveSpreadsheet();
      return Date.now() - start;
    },
    
    maxExecutionTime: () => {
      const start = Date.now();
      try {
        while(true) {
          if(Date.now() - start > 360000) break; // 6 min safety
          Utilities.sleep(1000);
        }
      } catch(e) {
        return {time: Date.now() - start, error: e.toString()};
      }
    },
    
    parallelRequests: () => {
      const urls = Array(50).fill('https://httpbin.org/delay/1');
      const start = Date.now();
      const responses = UrlFetchApp.fetchAll(urls);
      return {
        totalTime: Date.now() - start,
        parallelism: urls.length / ((Date.now() - start) / 1000)
      };
    }
  };
  
  return Object.entries(tests).map(([name, test]) => ({
    test: name,
    result: test()
  }));
}
```

RESULTS DISCOVERED:
- Cold start: 200-500ms (varies by services loaded)
- Max execution: 6 minutes (hard limit, 30 minutes for G Suite)
- Parallel requests: True parallelism up to 20 concurrent
- Memory limit: ~620MB available heap
- CPU throttling: Begins at 90 seconds cumulative

## Investigation 2: State Management

PROBING STATE LAYERS...

```javascript
function investigateStateManagement() {
  // Test 1: Request-scoped state
  global.requestState = global.requestState || 0;
  global.requestState++;
  
  // Test 2: Script Properties (persistent)
  PropertiesService.getScriptProperties().setProperty('counter', 
    String(Number(PropertiesService.getScriptProperties().getProperty('counter') || 0) + 1));
  
  // Test 3: Cache Service (temporary)
  const cache = CacheService.getScriptCache();
  cache.put('session_' + Session.getTemporaryActiveUserKey(), 'data', 21600);
  
  // Test 4: Lock Service (concurrency)
  const lock = LockService.getScriptLock();
  try {
    lock.waitLock(10000);
    // Critical section
  } finally {
    lock.releaseLock();
  }
  
  return {
    globalStatePersists: global.requestState > 1, // FALSE
    propertiesLimit: '500KB total, 9KB per value',
    cacheLimit: '100KB per key, 6 hours max',
    lockTimeout: '30 seconds max wait'
  };
}
```

DISCOVERED STATE PATTERNS:
- No request state persistence (each execution is isolated)
- Properties Service: Best for config/settings (500KB limit)
- Cache Service: Best for temporary data (100KB/key, 6hr)
- Lock Service: Required for concurrent writes
- Drive files: Unlimited storage but 10MB/write limit

## Investigation 3: Authentication Flow

TRACING AUTH PIPELINE...

```javascript
function investigateAuthentication() {
  const authModes = {
    installedAddon: () => {
      // Runs as user who installed
      return {
        user: Session.getActiveUser().getEmail(),
        effective: Session.getEffectiveUser().getEmail(),
        scopes: ScriptApp.getAuthorizationInfo(ScriptApp.AuthMode.FULL)
      };
    },
    
    webApp: () => {
      // Depends on deployment settings
      return {
        executeAs: 'USER_ACCESSING or USER_DEPLOYING',
        access: 'ANYONE, ANYONE_ANONYMOUS, DOMAIN, MYSELF',
        userIdentifiable: Session.getActiveUser().getEmail() !== ''
      };
    },
    
    trigger: () => {
      // Runs as trigger creator
      return {
        user: 'Trigger creator',
        effective: 'Trigger creator',
        scopes: 'Pre-authorized at trigger creation'
      };
    },
    
    apiExecutable: () => {
      // OAuth2 required
      return {
        auth: 'OAuth2 token required',
        scopes: 'Defined in manifest',
        user: 'Token owner'
      };
    }
  };
  
  return authModes;
}
```

AUTHENTICATION FINDINGS:
- Web Apps: Can't identify users unless domain-restricted
- Add-ons: Full user context but requires installation
- Triggers: Run as creator, not current user
- API Executable: Requires OAuth2 flow implementation

## Investigation 4: Platform Gotchas

ANALYZING GITHUB ISSUES & STACK OVERFLOW...

FATAL GOTCHAS DISCOVERED:
1. "PropertiesService randomly loses data"
   - Cause: Race conditions without locks
   - Solution: Always use LockService for writes

2. "Script stops working after 90 days"
   - Cause: Automatic trigger disabling
   - Solution: Implement heartbeat function

3. "Cannot debug triggers locally"
   - Cause: Triggers run in different context
   - Solution: Create test functions that simulate

4. "HTML Service forms timeout"
   - Cause: 5-minute limit for google.script.run
   - Solution: Implement client-side retry logic

5. "Quota errors with no quota exceeded"
   - Cause: Hidden sub-quotas (30 locks/sec)
   - Solution: Implement exponential backoff

## OUTPUT: Implementation Guide

### Boilerplate Template
```javascript
/**
 * Google Apps Script Boilerplate (from tech-investigator)
 */

// Global initialization (runs once per container)
const CONFIG = PropertiesService.getScriptProperties().getProperties();
const CACHE = CacheService.getScriptCache();

// Request handler with error boundaries
function doGet(e) {
  try {
    // Lock for concurrent request handling
    const lock = LockService.getScriptLock();
    lock.tryLock(10000);
    
    // Your logic here
    const result = handleRequest(e);
    
    lock.releaseLock();
    return ContentService.createTextOutput(JSON.stringify(result))
      .setMimeType(ContentService.MimeType.JSON);
  } catch(error) {
    console.error('Request failed:', error);
    return ContentService.createTextOutput(JSON.stringify({
      error: error.toString()
    })).setMimeType(ContentService.MimeType.JSON);
  }
}

// State management pattern
class StateManager {
  static get(key) {
    // Try cache first (fast)
    let value = CACHE.get(key);
    if (value) return JSON.parse(value);
    
    // Fall back to properties (persistent)
    value = PropertiesService.getScriptProperties().getProperty(key);
    if (value) {
      CACHE.put(key, value, 3600); // Re-cache
      return JSON.parse(value);
    }
    
    return null;
  }
  
  static set(key, value) {
    const lock = LockService.getScriptLock();
    try {
      lock.waitLock(10000);
      const serialized = JSON.stringify(value);
      
      // Write to both cache and properties
      CACHE.put(key, serialized, 3600);
      PropertiesService.getScriptProperties().setProperty(key, serialized);
    } finally {
      lock.releaseLock();
    }
  }
}

// Retry pattern for external APIs
function fetchWithRetry(url, params = {}, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return UrlFetchApp.fetch(url, params);
    } catch(e) {
      if (i === maxRetries - 1) throw e;
      Utilities.sleep(Math.pow(2, i) * 1000); // Exponential backoff
    }
  }
}
```

### Anti-Patterns to Avoid
```javascript
// ❌ NEVER: Assume global state persists
global.counter = (global.counter || 0) + 1; // Won't work

// ❌ NEVER: Write to Properties without lock
PropertiesService.getScriptProperties().setProperty('key', 'value'); // Race condition

// ❌ NEVER: Ignore execution time
while(true) { /* long process */ } // Will timeout at 6 minutes

// ❌ NEVER: Trust cache to persist
CacheService.getScriptCache().put('critical', data); // Can be evicted

// ❌ NEVER: Assume user identity in public web apps
Session.getActiveUser().getEmail(); // Returns empty for anonymous
```

INVESTIGATION COMPLETE
Duration: 2 hours
Artifacts Generated: 
- Implementation guide
- Boilerplate code
- Anti-pattern documentation
- Platform constraints map
```

## Example 2: Stack Selection for E-commerce

### Invocation
```bash
/prompt tech-investigator --mode select --context "E-commerce platform for handmade goods, 500 sellers, 10k products, social features"
```

### Agent Execution

```markdown
EXECUTING TECH-INVESTIGATOR: Select Mode
Context: E-commerce platform for handmade goods, 500 sellers, 10k products, social features

## Requirement Analysis
- Scale: Medium (500 sellers, 10k products)
- Type: Marketplace with social
- Special: User-generated content, payments, social engagement
- Performance: Standard e-commerce (2-3s page loads acceptable)

## Dimension 1: Frontend Framework

EVALUATING CANDIDATES...

### Minimalist: Vanilla JS + Alpine.js
GitHub Evidence:
- https://github.com/basecamp/hey (Basecamp email) - Alpine for interactions
- https://github.com/livewire/livewire (15k stars) - Server-side + Alpine
Verdict: Insufficient for social features ✗

### Standard: Next.js + React
GitHub Evidence:
- https://github.com/vercel/commerce (10k stars) - Next.js commerce
- https://github.com/medusajs/medusa (20k stars) - React e-commerce
- https://github.com/reactioncommerce/reaction (12k stars)
- https://github.com/spree/spree (12k stars) - React frontend option
- https://github.com/vendure-ecommerce/vendure (5k stars)
Verdict: Perfect fit, proven for e-commerce ✓

### Advanced: Remix + React
GitHub Evidence:
- https://github.com/remix-run/examples (2k stars) - E-commerce examples
- https://github.com/epicweb-dev/epic-stack (3k stars)
Verdict: Good but less e-commerce specific examples ≈

**Selected: Next.js + React**

## Dimension 2: Backend Architecture

### Minimalist: Node.js + Express
GitHub Evidence:
- https://github.com/nodejs/node (100k stars)
- https://github.com/expressjs/express (62k stars)
Verdict: Would require building everything ✗

### Standard: Node.js + Medusa.js/Vendure
GitHub Evidence:
- https://github.com/medusajs/medusa (20k stars) - Full e-commerce backend
- https://github.com/vendure-ecommerce/vendure (5k stars) - GraphQL commerce
- https://github.com/spree/spree_backend (12k stars)
- https://github.com/sylius/sylius (7k stars)
- https://github.com/prestashop/prestashop (7k stars)
Verdict: Purpose-built for e-commerce ✓

### Advanced: Microservices
Verdict: Over-engineered for 500 sellers ✗

**Selected: Medusa.js** (headless commerce platform)

## Dimension 3: Database

### Minimalist: PostgreSQL only
Verdict: Need additional stores for search/cache ✗

### Standard: PostgreSQL + Redis + Elasticsearch
GitHub Evidence:
- Every major e-commerce platform uses this trio
- https://github.com/elastic/elasticsearch (67k stars)
- https://github.com/redis/redis (64k stars)
Verdict: Industry standard for e-commerce ✓

### Advanced: Multi-model (Mongo + Graph)
Verdict: Unnecessary complexity ✗

**Selected: PostgreSQL + Redis + Elasticsearch**

## Dimension 4: Payment Processing

### Standard: Stripe + PayPal
GitHub Evidence:
- https://github.com/stripe/stripe-node (3k stars)
- All analyzed e-commerce repos use Stripe
Verdict: Industry standard ✓

## Dimension 5: Social Features

### Standard: Stream.io or custom with Socket.io
GitHub Evidence:
- https://github.com/GetStream/stream-chat-react (1k stars)
- https://github.com/socketio/socket.io (59k stars)
Verdict: Stream for speed, Socket.io for control ✓

## Compatibility Matrix

| Stack Component | Compatibility Score | Notes |
|-----------------|-------------------|--------|
| Next.js + Medusa.js | 10/10 | Designed to work together |
| Medusa + PostgreSQL | 10/10 | Native support |
| Next.js + Stream.io | 9/10 | React SDK available |
| Full Stack | 9.5/10 | Excellent compatibility |

## Migration Analysis from GitHub

DISCOVERED PATTERNS:
- WooCommerce → Medusa (common migration path)
- Shopify → Medusa (for customization needs)
- Custom → Medusa (to avoid rebuilding)

## Cost Analysis
- Medusa.js: Open source (free)
- PostgreSQL: $20-100/month (managed)
- Redis: $15-50/month (managed)
- Elasticsearch: $95/month (Elastic Cloud)
- Stream.io: $499/month (social features)
- Total: ~$650/month for infrastructure

## FINAL RECOMMENDATION

### Primary Stack
```yaml
frontend: Next.js 14 + React
backend: Medusa.js (headless commerce)
database: PostgreSQL (primary) + Redis (cache) + Elasticsearch (search)
payments: Stripe + PayPal
social: Stream.io (chat/activity feeds)
hosting: Vercel (frontend) + Railway (backend)
```

### Rationale
- Medusa.js provides complete e-commerce backend (inventory, orders, customers)
- Next.js proven for e-commerce with excellent performance
- Stack used by successful marketplaces (analysis of 15+ repos)
- Total development time: 3-4 months with this stack vs 8-10 months custom

### Alternative Stacks

**Conservative (Proven but older)**
```yaml
stack: WordPress + WooCommerce + custom plugins
when: "Team knows PHP, wants faster launch"
tradeoff: "Less scalable, harder to customize social features"
```

**Innovative (Modern but riskier)**
```yaml
stack: Shopify Hydrogen + Oxygen + custom apps
when: "Want Shopify ecosystem but custom frontend"
tradeoff: "Vendor lock-in, monthly Shopify fees"
```

SELECTION COMPLETE
Duration: 45 minutes
Evidence: 23 GitHub repositories analyzed
Confidence: 95% (strong GitHub evidence, proven migrations)
```

## Example 3: Hybrid Mode for Migration

### Invocation
```bash
/prompt tech-investigator --mode hybrid --context "Migrate Salesforce app to modern stack, keep CRM integration" --depth 2
```

### Agent Execution

```markdown
EXECUTING TECH-INVESTIGATOR: Hybrid Mode
Context: Migrate Salesforce app to modern stack, keep CRM integration
Depth: 2 candidates for deep investigation

## Phase 1: Stack Selection (30 minutes)

ANALYZING MIGRATION REQUIREMENTS...
- Current: Salesforce (Apex, Visualforce, Lightning)
- Need: Modern UI, keep CRM data, better performance
- Constraint: Maintain Salesforce as system of record

TOP 2 CANDIDATES IDENTIFIED:
1. Next.js + Salesforce APIs (JSforce)
2. Remix + Salesforce GraphQL

## Phase 2: Parallel Deep Investigation

### WORKTREE 1: Next.js + Salesforce APIs

EXECUTION MODEL TESTING:
```javascript
// Testing Salesforce API limits from Next.js
async function testSalesforceIntegration() {
  const conn = new jsforce.Connection({
    loginUrl: 'https://login.salesforce.com'
  });
  
  // Test API limits
  const limits = await conn.limits();
  console.log('Daily API Requests:', limits.DailyApiRequests);
  // Result: 15,000 for Developer Edition
  
  // Test query performance
  const start = Date.now();
  const result = await conn.query('SELECT Id FROM Account LIMIT 2000');
  console.log(`Query time: ${Date.now() - start}ms`);
  // Result: 800-1200ms for 2000 records
  
  // Test bulk operations
  const bulk = conn.bulk.load('Account', 'insert', accounts);
  // Result: 10,000 records/batch maximum
}
```

DISCOVERED CONSTRAINTS:
- API Limits: 15,000 calls/day (Dev), 5,000,000 (Enterprise)
- Query timeout: 120 seconds
- Bulk API: 10,000 records per batch
- Streaming API: Available for real-time updates

### WORKTREE 2: Remix + Salesforce GraphQL

EXECUTION MODEL TESTING:
```javascript
// Testing Salesforce GraphQL
async function testSalesforceGraphQL() {
  const query = `
    query accounts {
      uiapi {
        query {
          Account(first: 200) {
            edges {
              node {
                Id
                Name { value }
                AnnualRevenue { value }
              }
            }
          }
        }
      }
    }
  `;
  
  // Result: More efficient than REST (single request for related data)
  // But: Limited to UI API supported objects
}
```

## Phase 3: Synthesis

### Comparison Matrix
| Aspect | Next.js + JSforce | Remix + GraphQL |
|--------|------------------|-----------------|
| API Efficiency | Standard | Better (fewer calls) |
| Developer Experience | Excellent | Good |
| GitHub Examples | 20+ repos | 5 repos |
| Real-time Updates | Via Streaming API | Via Subscriptions |
| Learning Curve | Lower | Higher |

### FINAL RECOMMENDATION

**Selected: Next.js + Salesforce APIs (JSforce)**

```yaml
architecture:
  frontend: Next.js 14 App Router
  api_layer: Next.js API Routes + JSforce
  caching: Redis for Salesforce data
  auth: Salesforce OAuth 2.0
  realtime: Salesforce Streaming API
  
implementation_pattern: |
  // Caching layer to minimize API calls
  async function getSalesforceData(objectType, id) {
    const cached = await redis.get(`sf:${objectType}:${id}`);
    if (cached) return JSON.parse(cached);
    
    const data = await conn.sobject(objectType).retrieve(id);
    await redis.set(`sf:${objectType}:${id}`, JSON.stringify(data), 'EX', 300);
    return data;
  }

gotchas_discovered:
  - "SOQL injection is real - always parameterize"
  - "API limits hit fast with N+1 queries - batch everything"
  - "OAuth refresh tokens expire after 90 days of inactivity"
  - "Bulk API is async - need job polling mechanism"
  - "Formula fields can't be updated via API"
```

HYBRID INVESTIGATION COMPLETE
Duration: 3 hours
Mode Transitions: Select → Deep (2 parallel) → Synthesis
Confidence: High (tested actual API limits)
```

## Summary of Agent Capabilities

The tech-investigator agent provides:

1. **Three Execution Modes**
   - `select`: Choose from multiple technology options
   - `deep`: Understand specific platform deeply
   - `hybrid`: Select then investigate top candidates

2. **Evidence-Based Decisions**
   - Always analyzes 5+ GitHub repositories per technology
   - Tests actual platform limits with code
   - Extracts patterns from production systems

3. **Structured Output**
   - YAML format for easy parsing
   - Includes rationale and trade-offs
   - Provides implementation-ready code

4. **Integration with IDEAL-STI**
   - Replaces Phase 4 completely
   - Feeds constraints to Phase 5 (Requirements)
   - Provides patterns for Phase 7 (Architecture)
   - Documents decisions for Phase 8

5. **Parallel Execution**
   - Uses worktree isolation for concurrent investigations
   - Synthesizes findings from multiple deep dives
   - Maintains investigation isolation