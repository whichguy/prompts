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
Ask prompter to run stack-recommendation with context: "Build a real-time collaborative editor for 1000 users"
Ask prompter to run stack-recommendation with context: "E-commerce platform" and rehydration from ./previous-analysis/marketplace.yaml
```

**Output**: Primary technology stack, alternatives (conservative/innovative), GitHub evidence from 5+ repos, compatibility analysis, implementation guidance.

### platform-constraints  
**Produces**: Platform limitation analysis with empirical testing results  
**Solves**: "What are the real constraints and gotchas of this platform?"

```
Ask prompter to run platform-constraints with context: "Google Apps Script execution limits and state management"
Ask prompter to run platform-constraints with context: "Vercel Edge Functions" and focus areas: "database,scaling"
```

**Output**: Hard/soft limits discovered through testing, required patterns, anti-patterns, boilerplate code, use case recommendations.

### migration-strategy
**Produces**: Zero-downtime migration plan with risk mitigation  
**Solves**: "How do I safely migrate from my current system?"

```
Ask prompter to run migration-strategy with context: "Migrate PHP monolith to microservices, keep running during migration"
Ask prompter to run migration-strategy with context: "Modernize legacy Java system" and risk tolerance: conservative
```

**Output**: Phase-by-phase plan, zero-downtime approach, data synchronization strategy, risk assessment, rollback procedures.

### project-blueprint
**Produces**: Complete project implementation plan with phases and tasks  
**Solves**: "I need a comprehensive plan to build this project"

```
Ask prompter to run project-blueprint with context: "Build a SaaS project management tool for remote teams"
Ask prompter to run project-blueprint with context: "Mobile expense app" and constraints: "6 months, 2 person team"
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

```
# Cache results for future similar projects
Ask prompter to run stack-recommendation with context: "marketplace project" and save output to ./knowledge/marketplace-stack.yaml

# Rehydrate from previous analysis  
Ask prompter to run stack-recommendation with context: "another marketplace" and rehydration from ./knowledge/marketplace-stack.yaml
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
Ask prompter to run [prompt-name] with context: "[natural language description]"
```

### With Rehydration
```
Ask prompter to run [prompt-name] with context: "[description]" and rehydration from ./previous-analysis/file.yaml
```

### With Constraints
```
Ask prompter to run [prompt-name] with context: "[description]" and constraints: "budget, timeline, team size"
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
```
# 1. Get project plan
Ask prompter to run project-blueprint with context: "Build collaborative whiteboard app"

# 2. Get technology recommendations  
Ask prompter to run stack-recommendation with context: "real-time collaborative whiteboard for 100 users"

# 3. Analyze chosen platform constraints
Ask prompter to run platform-constraints with context: "Node.js with Socket.io scaling limits"

# 4. Create migration plan if replacing existing system
Ask prompter to run migration-strategy with context: "Migrate from Rails to Node.js whiteboard"
```

### Rehydration Chain
```
# Build organizational knowledge  
Ask prompter to run stack-recommendation with context: "collaborative tool" and save output to ./org-knowledge/collab-stack.yaml
Ask prompter to run platform-constraints with context: "WebSocket platforms" and save output to ./org-knowledge/websocket-limits.yaml

# Reuse for similar project with path references
Ask prompter to run project-blueprint with context: "team collaboration app" and rehydration from ./org-knowledge/collab-stack.yaml and additional context from ./org-knowledge/websocket-limits.yaml
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

## Prompter Agent Integration

All prompts are designed to work with the **prompter agent framework** for clean context isolation and proper parameter passing:

### **Syntax Pattern:**
```
Ask prompter to run [template-name] with context: "[natural language description]" [and additional parameters] [and save output to path]
```

### **Context Passing Examples:**
```
# Basic execution
Ask prompter to run stack-recommendation with context: "Build a mobile app for 10k users"

# With constraints
Ask prompter to run stack-recommendation with context: "Build a mobile app for 10k users" and constraints: "6 month timeline, iOS first, $50k budget"

# With rehydration (knowledge reuse)
Ask prompter to run stack-recommendation with context: "Build a marketplace app" and rehydration from ./knowledge/marketplace-analysis.yaml

# With multiple context sources
Ask prompter to run project-blueprint with context: "Build collaboration tool" and rehydration from ./knowledge/collab-stack.yaml and additional context from ./knowledge/team-constraints.yaml

# With output saving
Ask prompter to run platform-constraints with context: "Google Apps Script limits" and focus areas: "execution,storage" and save output to ./analysis/gas-constraints.yaml
```

### **Benefits:**
- **Clean Context**: Each prompt runs in isolation without polluting main context
- **Proper Parameter Passing**: Structured parameter handling via natural language
- **Path Management**: Explicit file path handling for rehydration and output
- **Agent Framework**: Leverages specialized prompter agent for template execution

This repository provides a foundation for outcome-driven, evidence-based software architecture decision making with organizational knowledge accumulation.