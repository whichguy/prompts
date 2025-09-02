# Prompts Repository

Collection of advanced prompt-as-code templates for Claude Code and other AI agents.

## Key Prompts

### tech-investigator
Advanced technology investigation agent using natural language interpretation for architecture selection and deep platform understanding.

**Features:**
- **Stack Selection Mode**: Evaluate multiple technology options with GitHub evidence
- **Deep Investigation Mode**: Test platform limits and discover patterns
- **Migration Mode**: Plan zero-downtime migrations from legacy systems
- **Natural Language**: No command-line flags, just describe what you need

**Usage:**
```bash
/prompt tech-investigator "Help me choose the best stack for a real-time collaborative editor"
/prompt tech-investigator "Deep dive into how Vercel Edge Functions handle database connections"
/prompt tech-investigator "Migrate from PHP monolith to modern microservices architecture"
```

### ideal-sti
Comprehensive 11-phase project planning system with parallel subagent orchestration.

**Features:**
- **11 Phases**: From discovery through deployment
- **Worktree Isolation**: Parallel execution with Git worktrees
- **Adaptive Complexity**: Scales to project size automatically
- **Tech Integration**: Uses tech-investigator for Phase 4

**Usage:**
```bash
/prompt ideal-sti "Build a real-time collaborative task management system"
```

## Philosophy

These prompts follow a **prompt-as-code** approach where:
- Natural language instructions guide behavior
- Pattern recognition determines execution mode
- Evidence requirements ensure rigor
- Adaptive depth based on context

## Structure

```
prompts/
├── tech-investigator-final.md    # Main tech investigation agent
├── ideal-sti.md                  # Complete planning system
├── ideal-sti-v3-natural-language.md  # Natural language enhancements
└── supporting files...            # Examples and integration guides
```

## Integration

These prompts are designed to work together:
- IDEAL-STI Phase 4 automatically invokes tech-investigator
- Tech-investigator provides constraints for subsequent IDEAL-STI phases
- Both use natural language for intuitive interaction

## Contributing

Feel free to submit improvements or new prompt templates that follow the prompt-as-code philosophy.