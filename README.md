# Outcome-Oriented Prompts Repository

Collection of stateless, outcome-focused prompt templates that produce structured deliverables for software architecture and project planning.

## Philosophy

These prompts follow an **outcome-oriented, prompt-as-code** approach:

- **Names describe what they produce**, not what they are
- **Stateless design** with clear input/output contracts  
- **Rehydration support** via `--rehydrate-from <path>` for organizational knowledge building
- **Structured YAML output** for composability and caching
- **Evidence-based recommendations** through GitHub analysis and empirical testing

## Core Prompts

### stack-recommendation
**Produces**: Technology stack recommendation with evidence-based rationale  
**Solves**: "What technologies should I use for this project?"

```
stack-recommendation "Build a real-time collaborative editor for 1000 users"
stack-recommendation "E-commerce platform" --rehydrate-from ./previous-analysis/marketplace.yaml
```

**Output**: Primary technology stack, alternatives (conservative/innovative), GitHub evidence from 5+ repos, compatibility analysis, implementation guidance.

### platform-constraints  
**Produces**: Platform limitation analysis with empirical testing results  
**Solves**: "What are the real constraints and gotchas of this platform?"

```
platform-constraints "Google Apps Script execution limits and state management"
platform-constraints "Vercel Edge Functions" --focus-areas "database,scaling"
```

**Output**: Hard/soft limits discovered through testing, required patterns, anti-patterns, boilerplate code, use case recommendations.

### migration-strategy
**Produces**: Zero-downtime migration plan with risk mitigation  
**Solves**: "How do I safely migrate from my current system?"

```
migration-strategy "Migrate PHP monolith to microservices, keep running during migration"
migration-strategy "Modernize legacy Java system" --risk-tolerance conservative
```

**Output**: Phase-by-phase plan, zero-downtime approach, data synchronization strategy, risk assessment, rollback procedures.

### project-blueprint
**Produces**: Complete project implementation plan with phases and tasks  
**Solves**: "I need a comprehensive plan to build this project"

```
project-blueprint "Build a SaaS project management tool for remote teams"
project-blueprint "Mobile expense app" --constraints "6 months, 2 person team"
```

**Output**: Detailed phases, task breakdown with estimates, team recommendations, technology stack, risk assessment, success metrics.

## Prompt Composability

Prompts are designed to work together:

```
project-blueprint → stack-recommendation → platform-constraints → architecture-design
                 ↘ migration-strategy → implementation-tasks
```

Each prompt's YAML output becomes structured input for related prompts.

## Rehydration System

All prompts support organizational knowledge reuse:

```bash
# Cache results for future similar projects
stack-recommendation "marketplace project" > ./knowledge/marketplace-stack.yaml

# Rehydrate from previous analysis  
stack-recommendation "another marketplace" --rehydrate-from ./knowledge/marketplace-stack.yaml
```

**Benefits**:
- **Speed**: Rehydrated analysis completes in minutes vs hours
- **Consistency**: Organizational technology patterns  
- **Learning**: Builds knowledge base over time
- **Quality**: Combines cached wisdom with fresh research

## Input/Output Contracts

Every prompt has standardized contracts:

### Input Contract
- `<prompt-context>`: Natural language project description
- `--rehydrate-from <path>`: Optional path to previous analysis
- Additional flags for constraints, focus areas, etc.

### Output Contract
```yaml
deliverable_type: [type of output]
generated_at: [timestamp]
confidence_level: [high|medium|low]
[structured deliverable content]
next_actions: [how to use this output]
```

## Quality Gates

Each prompt enforces quality standards:
- **stack-recommendation**: 5+ GitHub repos analyzed, compatibility matrix completed
- **platform-constraints**: Empirical testing performed, patterns documented  
- **migration-strategy**: Risk assessment complete, rollback plans defined
- **project-blueprint**: Phase breakdown with dependencies, effort estimates provided

## Usage Patterns

### Basic Usage
```
[prompt-name] "[natural language description]"
```

### With Rehydration
```
[prompt-name] "[description]" --rehydrate-from ./previous-analysis/file.yaml
```

### With Constraints
```
[prompt-name] "[description]" --constraints "budget, timeline, team size"
```

## Repository Structure

```
prompts/
├── stack-recommendation.md      # Technology stack selection
├── platform-constraints.md     # Platform limitation analysis  
├── migration-strategy.md       # System migration planning
├── project-blueprint.md        # Complete project planning
├── outcome-oriented-naming-analysis.md  # Design rationale
├── ideal-sti.md                # 11-phase project planning system
├── ideal-sti-knowledge-rehydration.md   # Knowledge persistence integration
├── ideal-sti-tech-investigator-integration.md  # Updated for outcome-oriented prompts
└── legacy/                     # Previous tech-investigator versions for reference
    ├── tech-investigator.md
    ├── tech-investigator-enhanced.md
    └── integration-examples/
```

## Integration Examples

### Waterfall Approach
```bash
# 1. Get project plan
project-blueprint "Build collaborative whiteboard app"

# 2. Get technology recommendations  
stack-recommendation "real-time collaborative whiteboard for 100 users"

# 3. Analyze chosen platform constraints
platform-constraints "Node.js with Socket.io scaling limits"

# 4. Create migration plan if replacing existing system
migration-strategy "Migrate from Rails to Node.js whiteboard"
```

### Rehydration Chain
```bash
# Build organizational knowledge
stack-recommendation "collaborative tool" > ./org-knowledge/collab-stack.yaml
platform-constraints "WebSocket platforms" > ./org-knowledge/websocket-limits.yaml

# Reuse for similar project
project-blueprint "team collaboration app" \
  --rehydrate-from ./org-knowledge/collab-stack.yaml
```

## IDEAL-STI Integration

The IDEAL-STI 11-phase planning system orchestrates outcome-oriented prompts:

**Phase 4 Integration**: Technology Research phase now uses:
- `stack-recommendation` for greenfield projects
- `platform-constraints` for specific platform investigations  
- `migration-strategy` for legacy system modernization
- `project-blueprint` for comprehensive project planning

**Knowledge Rehydration**: IDEAL-STI automatically:
- Caches prompt outputs in `docs/planning/.knowledge-state/`
- Reuses analysis via `--rehydrate-from` for similar projects
- Validates prompt outputs with quality gates
- Accumulates organizational knowledge over time

**Quality Gates**: Phase transitions validate:
- Required YAML structure and fields present
- Confidence levels meet thresholds
- Evidence requirements satisfied
- Integration patterns followed

This repository provides a foundation for outcome-driven, evidence-based software architecture decision making with organizational knowledge accumulation.