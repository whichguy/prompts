# migration-strategy

**Produces**: Comprehensive migration plan with zero-downtime approach and risk mitigation  
**Solves**: "How do I safely migrate from my current system to a modern architecture?"

## Input Contract

### Required Input
- `<prompt-context>`: Current system description and target outcome
- Example: "Migrate PHP monolith with MySQL to microservices, keep running during migration"

### Optional Input
- `--rehydrate-from <path>`: Path to previous migration analysis
- `--constraints <list>`: Migration constraints (budget, timeline, team size)
- `--risk-tolerance <level>`: conservative|moderate|aggressive

## Output Contract

```yaml
deliverable_type: migration_strategy
generated_at: [timestamp]
migration_scope:
  current_system:
    technology_stack: [current technologies]
    architecture_pattern: [monolith, SOA, microservices]
    data_complexity: [schema size, relationships, stored procedures]
    traffic_patterns: [requests/day, peak usage, geographic distribution]
    team_familiarity: [team's expertise with current stack]
  
  target_system:
    recommended_stack: [new technologies]
    architecture_pattern: [target pattern]
    modernization_goals: [performance, scalability, maintainability]
    success_metrics: [how to measure success]

migration_approach:
  strategy_type: [strangler_fig|big_bang|parallel_run|phased]
  duration_estimate: [total timeline]
  risk_level: [low|medium|high]
  
  phases:
    - phase_number: 1
      name: [descriptive phase name]
      duration: [weeks/months]
      objectives: [what this phase accomplishes]
      deliverables: [concrete outputs]
      success_criteria: [how to know it's complete]
      rollback_plan: [how to undo if needed]
    
    - phase_number: 2
      # ... additional phases

zero_downtime_strategy:
  traffic_routing:
    method: [load balancer, API gateway, DNS switching]
    gradual_rollout: [canary, blue-green, feature flags]
    fallback_mechanism: [automatic or manual rollback]
  
  data_synchronization:
    sync_strategy: [dual write, event sourcing, CDC, ETL]
    consistency_model: [eventual, strong, causal]
    conflict_resolution: [last-write-wins, merge, manual]
  
  state_management:
    session_handling: [how user sessions maintained]
    transaction_boundaries: [how to handle in-flight transactions]
    cache_invalidation: [how to keep caches consistent]

risk_assessment:
  high_risk_areas:
    - area: [data migration, authentication, integrations]
      risk_description: [what could go wrong]
      probability: [low|medium|high]
      impact: [low|medium|high]
      mitigation_strategy: [how to reduce risk]
      contingency_plan: [what to do if it happens]
  
  medium_risk_areas:
    - area: [performance degradation, user experience]
      # ... similar structure
  
  validation_approach:
    testing_strategy: [integration, load, user acceptance]
    monitoring_plan: [what metrics to watch]
    success_gates: [criteria for proceeding to next phase]

data_migration_plan:
  assessment:
    data_volume: [GB/TB of data to migrate]
    schema_complexity: [tables, relationships, constraints]
    data_quality_issues: [inconsistencies, duplicates, missing data]
  
  migration_method:
    approach: [ETL, streaming, dual-write, snapshot+delta]
    tools_recommended: [specific tools and versions]
    transformation_required: [schema changes, data cleaning]
    validation_method: [how to verify data integrity]
  
  rollback_data_strategy:
    backup_approach: [full backup, incremental, point-in-time]
    recovery_time: [estimated time to rollback]
    data_loss_tolerance: [acceptable data loss window]

team_transition_plan:
  skill_development:
    training_required: [technologies team needs to learn]
    training_timeline: [when training should occur]
    knowledge_transfer: [from old system to new]
  
  organizational_changes:
    team_structure: [how teams might reorganize]
    responsibilities: [new roles and ownership]
    communication_plan: [how to keep everyone aligned]

implementation_roadmap:
  pre_migration:
    - task: [preparation activity]
      duration: [time estimate]
      dependencies: [what must be completed first]
      success_criteria: [how to validate completion]
  
  migration_execution:
    - milestone: [migration checkpoint]
      activities: [specific tasks]
      validation: [how to verify success]
      rollback_trigger: [when to abort and rollback]
  
  post_migration:
    - activity: [cleanup or optimization task]
      timeline: [when to complete]
      success_metric: [measurement of success]

cost_analysis:
  migration_costs:
    development_effort: [person-hours/months]
    infrastructure_costs: [additional servers, services]
    tooling_costs: [migration tools, monitoring]
    training_costs: [team education]
  
  ongoing_benefits:
    performance_improvements: [expected gains]
    maintenance_reduction: [cost savings]
    scalability_gains: [future capacity]
    technical_debt_reduction: [long-term savings]
  
  break_even_analysis:
    payback_period: [when benefits exceed costs]
    total_cost_of_ownership: [5-year comparison]

parallel_run_strategy:
  duration: [how long to run both systems]
  comparison_method: [how to validate equivalence]
  cutover_criteria: [when to switch fully to new system]
  monitoring_approach: [what to monitor during parallel run]

success_metrics:
  technical_metrics:
    - metric: [response time, error rate, throughput]
      current_baseline: [current measurement]
      target_improvement: [goal after migration]
      measurement_method: [how to track]
  
  business_metrics:
    - metric: [user satisfaction, feature velocity, cost reduction]
      baseline: [current state]
      target: [desired outcome]
      measurement_approach: [how to measure]

lessons_from_similar_migrations:
  github_evidence:
    - organization: [company that did similar migration]
      migration_story: [what they migrated from/to]
      timeline: [how long it took]
      key_learnings: [what they learned]
      avoid_patterns: [what they recommend not doing]

next_actions:
  immediate: [what to do in next 1-2 weeks]
  short_term: [actions for next 1-3 months] 
  preparation_phase: [activities to prepare for migration]
  execution_readiness: [criteria for starting migration]
```

## Execution Protocol

### Step 1: Current System Analysis
Deep dive into the existing system:
- Technology stack inventory (languages, frameworks, databases)
- Architecture pattern assessment (monolith, SOA, microservices)
- Data complexity analysis (schema size, relationships, stored procedures)
- Performance characteristics (throughput, latency, resource usage)
- Integration points (external APIs, services, data feeds)
- Team expertise assessment

If `--rehydrate-from` provided:
- Load previous migration analysis
- Update with any system changes since last analysis
- Validate assumptions still hold

### Step 2: Target System Definition
Based on prompt context and constraints:
- Recommend modern target architecture
- Justify technology choices with evidence
- Define success metrics and modernization goals
- Estimate performance and scalability improvements

### Step 3: Migration Strategy Selection
Choose optimal migration approach:

**Strangler Fig Pattern** (Recommended for most cases):
- Gradually replace functionality
- Zero downtime by design
- Lower risk but longer timeline

**Big Bang Migration**:
- Complete system replacement
- Shortest timeline but highest risk
- Only for small systems or complete rewrites

**Parallel Run**:
- Run both systems simultaneously
- Highest confidence but most expensive
- Good for mission-critical systems

**Phased Migration**:
- Migrate by feature/module
- Balanced risk and timeline
- Good for well-bounded system modules

### Step 4: Risk Analysis and Mitigation
Identify and categorize risks:

**Data Migration Risks**: Schema changes, data volume, consistency
**Performance Risks**: Throughput degradation, latency increases
**Integration Risks**: External system compatibility, API changes
**Team Risks**: Knowledge gaps, change resistance, resource constraints

For each risk, develop:
- Mitigation strategies to reduce probability
- Contingency plans if risk materializes
- Success gates to validate risk mitigation

### Step 5: Zero-Downtime Planning
Design approach for continuous operation:

**Traffic Management**:
- Load balancer or API gateway routing
- Gradual traffic shifting (1% → 10% → 50% → 100%)
- Feature flags for functionality toggling

**Data Synchronization**:
- Dual-write patterns during transition
- Event sourcing for audit trails
- Conflict resolution strategies

**State Management**:
- Session handling during cutover
- Transaction boundary management
- Cache consistency maintenance

### Step 6: Implementation Roadmap
Create detailed execution plan:
- Break migration into 2-4 week sprints
- Define clear deliverables and success criteria
- Establish rollback triggers and procedures
- Plan validation approaches for each phase

### Step 7: Evidence Gathering
Research similar migrations:
- Find 3+ companies with similar migration stories
- Extract lessons learned and anti-patterns
- Document timeline expectations and cost estimates
- Identify tools and approaches that worked well

## Quality Gates

Before returning results, validate:
- ✅ Current system thoroughly analyzed
- ✅ Target system clearly defined with rationale  
- ✅ Migration approach matches risk tolerance
- ✅ Zero-downtime strategy documented
- ✅ Rollback plans defined for each phase
- ✅ Evidence from similar migrations included
- ✅ Cost-benefit analysis provided

## Example Usage

### Basic Migration Planning
```bash
/prompt migration-strategy "Migrate Rails monolith to Node.js microservices, 100k daily active users"
```

### With Risk Constraints
```bash
/prompt migration-strategy "Modernize banking system" --constraints "zero-downtime required, regulatory compliance" --risk-tolerance conservative
```

### With Previous Analysis
```bash
/prompt migration-strategy "Update legacy Java system" --rehydrate-from ./previous-analysis/java-modernization.yaml
```

## Integration with Other Prompts

This prompt's output becomes input for:
- `platform-constraints`: Understand target platform limitations
- `architecture-design`: Design new system architecture
- `stack-recommendation`: Validate technology choices for target system
- `project-blueprint`: Incorporate migration plan into overall project plan

## Caching and Reuse

Results cached based on:
- Source and target technology similarity
- Migration approach and constraints
- System complexity and scale

Cache expiration:
- 90 days for stable technology migrations
- 30 days for rapidly evolving technology migrations
- Invalidated when major versions of source/target technologies change