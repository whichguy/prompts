# Outcome-Oriented Prompt Naming Analysis

## Current Tool-Focused Names vs Outcome-Oriented Alternatives

### Current Naming Problems
- **tech-investigator** → What tool it is, not what it produces
- **ideal-sti** → Acronym that doesn't describe output
- Focus on methodology rather than deliverable

### Outcome-Oriented Naming Strategy

#### Stack Selection & Technology Decisions
**Current**: tech-investigator (stack selection mode)
**Outcome-Oriented Options**:
- `stack-recommendation` ✓ - Produces technology stack recommendation
- `architecture-blueprint` - Produces complete architecture design
- `technology-decision` - Produces technology choices with rationale

#### Deep Platform Analysis
**Current**: tech-investigator (deep investigation mode)  
**Outcome-Oriented Options**:
- `platform-constraints` ✓ - Discovers and documents platform limits
- `implementation-guide` - Produces how-to-implement documentation
- `platform-analysis` - Produces deep technical analysis

#### Migration Planning
**Current**: tech-investigator (migration mode)
**Outcome-Oriented Options**:
- `migration-strategy` ✓ - Produces migration plan and timeline
- `modernization-roadmap` - Produces step-by-step modernization plan
- `legacy-transition` - Produces transition approach

#### Project Planning
**Current**: ideal-sti
**Outcome-Oriented Options**:
- `project-blueprint` ✓ - Produces complete project plan
- `development-roadmap` - Produces development timeline and phases
- `implementation-plan` - Produces actionable implementation steps

## Recommended Outcome-Focused Names

### Primary Prompts
1. **stack-recommendation** - "I need technology recommendations for [project]"
2. **platform-constraints** - "I need to understand [platform] limitations"  
3. **migration-strategy** - "I need to migrate from [X] to [Y]"
4. **project-blueprint** - "I need a complete plan for [project]"

### Specialized Prompts
5. **architecture-design** - Produces system architecture diagrams and decisions
6. **integration-patterns** - Produces how systems should connect
7. **scaling-analysis** - Produces performance and scaling recommendations
8. **security-assessment** - Produces security requirements and patterns

## Stateless Input/Output Contract Design

### Template Structure
```markdown
# [prompt-name]

**Produces**: [Clear outcome description]
**Solves**: [Problem it addresses]

## Input Contract
- `<prompt-context>`: [What context is needed]
- `--rehydrate-from`: [Optional path to previous analysis]

## Output Contract
```yaml
deliverable_type: [type]
confidence_level: [high|medium|low]
evidence_sources: [list]
recommendations: [structured output]
next_actions: [what to do with this]
```

## Execution Protocol
[Stateless instructions]
```

### Example: stack-recommendation

```markdown
# stack-recommendation

**Produces**: Technology stack recommendation with evidence and rationale
**Solves**: "What technologies should I use for this project?"

## Input Contract
- `<prompt-context>`: Project description, scale, constraints
- `--rehydrate-from`: Optional path to similar project analysis

## Output Contract
```yaml
deliverable_type: stack_recommendation
confidence_level: high
evidence_sources:
  - github_repos: [5+ analyzed repositories]
  - production_examples: [companies using this stack]
  - benchmarks: [performance data]
recommendations:
  primary_stack:
    frontend: [technology]
    backend: [technology] 
    database: [technology]
    rationale: [why this optimizes for requirements]
  alternatives:
    conservative: [boring but proven option]
    innovative: [modern but riskier option]
next_actions:
  - Use output for architecture design
  - Reference constraints in requirements phase
  - Cache for similar future projects
```

## Execution Protocol
When invoked, analyze project context to determine scale, type, and constraints.
Research 5+ GitHub repositories with 1000+ stars for each technology candidate.
Score compatibility across dimensions: performance, complexity, ecosystem.
Provide structured recommendation with clear rationale and evidence.
```

## Benefits of Outcome-Oriented Naming

1. **Clear Value Proposition**: Users know what they'll get
2. **Composability**: Outputs become inputs to other prompts
3. **Caching**: Easy to cache by outcome type
4. **Discovery**: Users can find prompts by what they need to produce
5. **Validation**: Clear criteria for successful output

## Migration Path

### Phase 1: Rename Existing
- `tech-investigator.md` → `stack-recommendation.md`
- `ideal-sti.md` → `project-blueprint.md`

### Phase 2: Split by Outcome
- Extract deep investigation → `platform-constraints.md`
- Extract migration planning → `migration-strategy.md`

### Phase 3: Add Specialized
- `architecture-design.md` for system design
- `integration-patterns.md` for API/service design
- `scaling-analysis.md` for performance planning