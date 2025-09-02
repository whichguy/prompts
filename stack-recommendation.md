# stack-recommendation

**Produces**: Technology stack recommendation with evidence-based rationale  
**Solves**: "What technologies should I use for this project?"

## Input Contract

### Required Input
- `<prompt-context>`: Project description including scale, features, and constraints
- Example: "Build a real-time collaborative editor for 1000 users with React team"

### Optional Input  
- `--rehydrate-from <path>`: Path to previous similar analysis for context
- `--constraints <list>`: Explicit technical constraints (budget, compliance, etc.)
- `--team-skills <list>`: Team's existing technology expertise

## Output Contract

```yaml
deliverable_type: stack_recommendation
generated_at: [timestamp]
confidence_level: [high|medium|low]
context_analyzed:
  project_type: [web app|mobile app|api|desktop app]
  scale_estimate: [users, requests/sec, data volume]
  key_features: [real-time, collaboration, AI, etc.]
  constraints: [budget, timeline, compliance, team skills]

evidence_sources:
  github_analysis:
    repos_analyzed: [5+ repositories with 1000+ stars each]
    migration_patterns: [what stacks companies moved from/to]
    production_lessons: [extracted from issues, discussions]
  benchmarks:
    performance_data: [load testing results, latency measurements]
    scaling_examples: [companies at similar scale]

recommendations:
  primary_stack:
    frontend: [technology with version]
    backend: [technology with version]
    database: [primary data store]
    cache: [caching solution if needed]
    infrastructure: [deployment platform]
    rationale: |
      Detailed explanation of why this combination
      optimizes for the specific requirements
    
  alternatives:
    conservative:
      stack: [proven but potentially outdated technologies]
      when_to_use: "Choose if team lacks experience or timeline is tight"
      trade_offs: [what you gain vs what you lose]
    
    innovative:
      stack: [cutting-edge but potentially risky technologies]
      when_to_use: "Choose if competitive advantage matters more than stability"
      trade_offs: [what you gain vs what you lose]

compatibility_analysis:
  language_boundaries: [serialization costs, type safety]
  deployment_complexity: [how many moving parts]
  operational_overhead: [monitoring, debugging, scaling]
  learning_curve: [team ramp-up time]
  ecosystem_maturity: [library availability, community support]

implementation_guidance:
  setup_priority: [which components to build first]
  integration_points: [how components connect]
  testing_strategy: [how to validate the stack works]
  monitoring_approach: [what to observe in production]

next_actions:
  - Use this recommendation in architecture design phase
  - Reference constraints for requirements gathering
  - Cache analysis for similar future projects
  - Plan team training for new technologies
```

## Execution Protocol

### Step 1: Context Analysis
Parse the `<prompt-context>` to extract:
- Project type and primary use case
- Scale requirements (users, data, performance)
- Key features requiring specific technologies
- Explicit and implicit constraints

If `--rehydrate-from` is provided:
- Load previous analysis from specified path
- Compare project characteristics for reusability
- Note similarities and differences in requirements

### Step 2: Requirement Categorization
Classify project needs across dimensions:
- **Frontend Needs**: UI complexity, real-time updates, mobile support
- **Backend Needs**: API patterns, business logic, integration complexity  
- **Data Needs**: CRUD operations, analytics, search, real-time sync
- **Infrastructure Needs**: Scale, availability, geographic distribution
- **Special Needs**: AI/ML, compliance, real-time, multimedia

### Step 3: Technology Research
For each dimension, identify 3 candidates:
- **Minimalist**: Simplest option that meets requirements
- **Standard**: Most common industry choice
- **Advanced**: Future-proof but complex option

Research Requirements:
- Find 5+ GitHub repositories (1000+ stars) using each technology
- Analyze repository patterns: file structure, configuration, dependencies
- Extract lessons from GitHub Issues and Discussions
- Document migration patterns (what companies moved FROM and TO)
- Find performance benchmarks and production examples

### Step 4: Compatibility Scoring
Create compatibility matrix scoring 1-10:
- **Language Compatibility**: Type safety, serialization overhead
- **Deployment Simplicity**: Single vs multiple deployment targets
- **Operational Complexity**: Number of systems to monitor
- **Team Alignment**: Match with existing skills
- **Ecosystem Health**: Library availability, update frequency

### Step 5: Evidence Synthesis
Consolidate research into recommendation:
- Primary stack optimizing for requirements
- Conservative alternative for risk aversion  
- Innovative alternative for competitive advantage
- Clear rationale linking requirements to technology choices
- Honest assessment of trade-offs and risks

### Step 6: Implementation Roadmap
Provide actionable next steps:
- Component build order (database → API → frontend)
- Integration testing approach
- Team skill development plan
- Monitoring and observability setup

## Quality Gates

Before returning results, validate:
- ✅ Minimum 5 GitHub repositories analyzed per major technology
- ✅ Compatibility matrix completed with numerical scores
- ✅ Primary recommendation maps clearly to requirements
- ✅ Trade-offs honestly documented
- ✅ Alternative options provided for different risk profiles
- ✅ Implementation guidance actionable

## Example Usage

### Basic Usage
```bash
/prompt stack-recommendation "Build a document collaboration platform for 500 lawyers requiring audit trails and compliance"
```

### With Rehydration
```bash  
/prompt stack-recommendation "Build a marketplace for freelance developers" --rehydrate-from ./previous-analysis/marketplace-investigation.yaml
```

### With Constraints
```bash
/prompt stack-recommendation "Build a real-time dashboard" --constraints "PHP team, on-premise only, <$10k budget"
```

## Integration with Other Prompts

This prompt's output becomes input for:
- `architecture-design`: Use stack to design system components
- `platform-constraints`: Deep dive into chosen platform limitations
- `migration-strategy`: Plan migration from current to recommended stack
- `project-blueprint`: Incorporate technology decisions into project plan

## Caching and Reuse

Results are cached based on:
- Project type similarity (web app, mobile, API)
- Scale similarity (±50% user count)
- Feature overlap (>70% feature match)

Cache expiration:
- 30 days for exact project match
- 14 days for similar project patterns
- 7 days if technology ecosystem shows rapid change