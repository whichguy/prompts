# platform-constraints

**Produces**: Comprehensive analysis of platform limitations, quotas, and implementation patterns  
**Solves**: "What are the real constraints and gotchas of this technology platform?"

## Input Contract

### Required Input
- `<prompt-context>`: Platform name and investigation focus
- Example: "Google Apps Script execution limits and state management patterns"

### Optional Input
- `--rehydrate-from <path>`: Path to previous platform analysis
- `--focus-areas <list>`: Specific areas to investigate (execution, storage, auth, etc.)
- `--scale-target <number>`: Target user/request scale for limit testing

## Output Contract

```yaml
deliverable_type: platform_constraints
generated_at: [timestamp]
platform_analyzed: [technology name and version]
investigation_duration: [time spent on analysis]
confidence_level: [high|medium|low based on testing depth]

execution_model:
  runtime_environment: [V8, Node.js, JVM, etc.]
  concurrency_model: [single-threaded, event-loop, multi-threaded]
  test_code_executed: |
    [Actual code used to probe platform limits]
  empirical_findings:
    cold_start_time: [measured milliseconds]
    max_execution_time: [measured seconds/minutes]
    memory_limit: [measured MB]
    cpu_throttling: [when and how much]
    concurrent_requests: [maximum parallel operations]

storage_constraints:
  state_layers:
    request_state:
      persistence: [none|memory|disk]
      lifetime: [request duration|session|permanent]
    session_state:
      storage_location: [client|server|distributed]
      size_limit: [KB/MB limit]
      expiration: [timeout behavior]
    application_state:
      shared_access: [read-only|read-write|locked]
      synchronization: [how conflicts resolved]
    persistent_state:
      storage_type: [database|file|object store]
      size_limits: [per-record and total]
      query_limitations: [SQL subset, NoSQL patterns]

networking_model:
  protocols_supported: [HTTP, WebSocket, TCP, UDP]
  connection_limits: [concurrent connections]
  request_size_limits: [payload limits]
  response_streaming: [supported patterns]
  external_api_access: [restrictions and quotas]

authentication_flow:
  supported_methods: [OAuth, JWT, API keys, etc.]
  token_storage: [where tokens persist]
  session_management: [how sessions maintained]
  permission_evaluation: [when/where checked]
  security_boundaries: [what's isolated]

discovered_quotas:
  hard_limits:
    - quota_type: [API calls, storage, execution time]
      limit_value: [numerical limit]
      scope: [per user, per app, per second]
      test_evidence: [how discovered]
      workaround: [if any exists]
  
  soft_limits:
    - limit_type: [performance degradation points]
      threshold: [when performance degrades]
      impact: [how performance affected]
      mitigation: [how to optimize]

production_patterns:
  required_patterns:
    - pattern_name: [initialization, error handling, etc.]
      code_example: |
        [Copy-paste ready code that MUST be used]
      reason_required: [why this pattern is mandatory]
      failure_consequence: [what happens without it]
  
  anti_patterns:
    - pattern_name: [common mistake pattern]
      code_example: |
        [Code that looks right but fails]
      failure_mode: [how/when it fails]
      github_evidence: [link to issue demonstrating failure]

implementation_guide:
  boilerplate_code: |
    [Complete working starter template]
  setup_checklist:
    - [Step-by-step setup instruction]
    - [Configuration requirement]
    - [Testing verification step]
  
  testing_approach: |
    [How to test applications on this platform]
    [Platform-specific testing considerations]
  
  monitoring_strategy: |
    [What metrics to track]
    [How to debug issues]
    [Performance monitoring approach]

github_evidence:
  repositories_analyzed: [3+ production repos using platform]
  pattern_frequency: [how often patterns appear across repos]
  issue_analysis:
    common_failures: [what frequently breaks]
    workarounds_discovered: [community solutions]
    evolution_patterns: [how usage patterns changed over time]

limitations_for_use_cases:
  excellent_for:
    - [Use case where platform excels]
    - [Specific scenarios that work well]
  acceptable_for:
    - [Use cases that work but have trade-offs]
    - [Scenarios requiring workarounds]
  poor_choice_for:
    - [Use cases where platform fails]
    - [Scenarios to avoid]

scaling_analysis:
  bottleneck_identification:
    primary_bottleneck: [what limits scale first]
    secondary_bottleneck: [next limitation encountered]
  
  scaling_patterns:
    horizontal_scaling: [how to add more instances]
    vertical_scaling: [how to increase per-instance capacity]
    caching_strategies: [what and how to cache]
  
  cost_implications:
    free_tier_limits: [what's included free]
    scaling_costs: [how costs increase with usage]
    cost_optimization: [how to minimize expenses]

next_actions:
  - Reference these constraints in architecture design
  - Use boilerplate for rapid prototyping
  - Apply patterns in implementation phase
  - Monitor identified metrics in production
```

## Execution Protocol

### Step 1: Platform Profiling
Analyze the specified platform:
- Identify runtime environment and architecture
- Determine execution model (synchronous, asynchronous, event-driven)
- Map available APIs and services
- Note version differences if applicable

If `--rehydrate-from` provided:
- Load previous platform analysis
- Check for version changes that invalidate findings
- Identify gaps in previous investigation

### Step 2: Empirical Testing
Create and execute test code to discover actual limits:

```javascript
// Platform limit discovery template
function investigatePlatformLimits() {
  const tests = {
    executionTime: () => measureMaxExecutionDuration(),
    memoryUsage: () => allocateUntilLimit(),
    concurrency: () => testParallelOperations(),
    storageQuotas: () => writeUntilQuotaExceeded(),
    networkLimits: () => testRequestSizeLimits()
  };
  
  return Object.entries(tests).map(([name, test]) => ({
    test: name,
    result: test(),
    timestamp: Date.now()
  }));
}
```

### Step 3: Production Pattern Mining
Analyze 3+ GitHub repositories (500+ stars) using the platform:
- Extract common initialization patterns
- Identify configuration approaches
- Document error handling strategies
- Note performance optimizations
- Collect workarounds from Issues and Discussions

Search patterns:
- "doesn't work in production"
- "fails when deployed"
- "workaround for [platform]"
- "[platform] best practices"

### Step 4: State Management Analysis
Map how the platform handles different types of state:
- Request-scoped (dies after response)
- Session-scoped (persists across requests)
- Application-scoped (shared between users)
- Persistent (survives restarts)

Test state boundaries and synchronization mechanisms.

### Step 5: Integration Boundary Discovery
Document how platform connects to external systems:
- Database connection patterns and limitations
- API calling patterns and rate limits
- Authentication integration approaches
- File system access (if any)

### Step 6: Constraint Synthesis
Consolidate findings into actionable constraints:
- Hard limits that cannot be exceeded
- Soft limits where performance degrades
- Required patterns for successful operation
- Anti-patterns that cause failures

## Quality Gates

Before returning results, validate:
- ✅ Empirical testing performed with actual code
- ✅ At least 3 production repositories analyzed
- ✅ Both required patterns and anti-patterns documented
- ✅ Boilerplate code provided and tested
- ✅ Clear use case recommendations (good/acceptable/poor)
- ✅ Cost and scaling implications analyzed

## Example Usage

### Basic Platform Investigation
```
platform-constraints "Vercel Edge Functions database connection handling and limits"
```

### Focused Investigation
```
platform-constraints "Google Apps Script" --focus-areas "execution,storage,auth" --scale-target 1000
```

### With Previous Analysis
```
platform-constraints "AWS Lambda cold starts" --rehydrate-from ./previous-analysis/lambda-2023.yaml
```

## Integration with Other Prompts

This prompt's output becomes input for:
- `stack-recommendation`: Constraint information influences technology choices
- `architecture-design`: Platform limits shape system design decisions
- `migration-strategy`: Understanding current platform constraints motivates migration
- `implementation-plan`: Platform patterns become implementation requirements

## Caching and Reuse

Results cached based on:
- Platform name and major version
- Investigation focus areas
- Scale targets (similar if within 2x)

Cache expiration:
- 60 days for stable platforms (AWS, Google Cloud)
- 30 days for evolving platforms (Vercel, Cloudflare)
- 7 days if platform shows rapid feature changes