# IDEAL-STI v3 - Natural Language Integration

## Enhanced Phase 4 with Natural Language Tech-Investigator

### Previous Approach (v2)
```bash
# Command-line style with flags
/prompt tech-investigator --mode select --context "$arguments"
/prompt tech-investigator --mode deep --stack "google-apps-script"
```

### New Approach (v3) - Natural Language
```bash
# Natural language that expresses intent
execute_phase_4() {
    local project_description="$1"
    echo "=== Phase 4: Technology Investigation ==="
    
    # Build natural language prompt based on context
    local investigation_prompt=""
    
    # Check project type from previous phases
    if grep -q "greenfield" docs/planning/phase1-discovery.md 2>/dev/null; then
        investigation_prompt="Help me choose the best technology stack for: $project_description"
    elif grep -q "migration" docs/planning/phase1-discovery.md 2>/dev/null; then
        investigation_prompt="I need to migrate: $project_description. Evaluate options and show me how."
    elif grep -q "Platform:" docs/planning/phase3-feasibility.md 2>/dev/null; then
        local platform=$(grep "Platform:" docs/planning/phase3-feasibility.md | cut -d: -f2)
        investigation_prompt="Deep dive into how $platform handles $project_description"
    else
        investigation_prompt="Investigate technology options for: $project_description"
    fi
    
    # Launch tech-investigator with natural language
    echo "🔬 Launching investigation: $investigation_prompt"
    /prompt tech-investigator "$investigation_prompt" > docs/planning/phase4-investigation.md
    
    # Validate results
    validate_phase "4"
}
```

## Phase Execution with Natural Language Prompts

### Phase 1: Discovery with Natural Context
```markdown
execute_phase_1() {
    local user_request="$1"
    
    # Launch discovery with natural language
    local discovery_prompt="Explore and elaborate on this project idea: $user_request. 
    Find hidden stakeholders, edge cases, and requirements that weren't mentioned. 
    What similar projects exist and what can we learn from them?"
    
    /prompt discovery-agent "$discovery_prompt" > docs/planning/phase1-discovery.md
}
```

### Phase 3: Feasibility with Conversational Assessment
```markdown
execute_phase_3() {
    local context="$1"
    
    # Natural language feasibility check
    local feasibility_prompt="Based on what we've discovered about $context, 
    assess if this is technically feasible. What are the major risks? 
    What resources would we need? Are there any deal-breakers?"
    
    /prompt feasibility-agent "$feasibility_prompt" > docs/planning/phase3-feasibility.md
}
```

### Phase 7: Architecture with Intent-Based Design
```markdown
execute_phase_7() {
    # Read tech decisions from Phase 4
    local tech_stack=$(grep -A 5 "Recommended Stack" docs/planning/phase4-investigation.md)
    
    # Natural language architecture request
    local architecture_prompt="Design the system architecture using $tech_stack. 
    Show me how the components connect, how data flows, and where the complexity lives. 
    Include patterns we must follow and anti-patterns to avoid."
    
    /prompt architecture-agent "$architecture_prompt" > docs/planning/phase7-architecture.md
}
```

## Intelligent Context Building

Instead of passing flags, each phase builds context naturally:

```bash
# The orchestrator builds context as it progresses
execute_ideal_sti() {
    local initial_request="$1"
    local context="$initial_request"
    
    # Phase 1: Discovery adds context
    execute_phase_1 "$context"
    context="$context. $(extract_key_findings phase1-discovery.md)"
    
    # Phase 2: Goals refine context
    execute_phase_2 "$context"
    context="$context. Goals: $(extract_goals phase2-intent.md)"
    
    # Phase 3: Feasibility adds constraints
    execute_phase_3 "$context"
    context="$context. Constraints: $(extract_constraints phase3-feasibility.md)"
    
    # Phase 4: Technology investigation with full context
    local tech_prompt="Given this project context: $context, investigate the best technology approach"
    /prompt tech-investigator "$tech_prompt"
    
    # Context keeps building through all phases...
}

extract_key_findings() {
    # Pull the most important discoveries to add to context
    grep -A 2 "Key Findings\|Critical Discovery\|Must Have" "docs/planning/$1" | 
    head -3 | 
    tr '\n' ' '
}
```

## Agent Invocation Evolution

### Old Style (Explicit Arguments)
```bash
/prompt tech-research-analyst \
  --dimension "frontend" \
  --requirements "real-time collaboration" \
  --scale "1000 users" \
  --mode "research"
```

### New Style (Natural Language)
```bash
/prompt tech-investigator "We need real-time collaboration for 1000 users, what frontend framework should we use?"
```

### Benefits of Natural Language Approach

1. **More Intuitive**: Users express needs naturally
2. **Context-Aware**: Previous discoveries influence interpretation  
3. **Adaptive Depth**: Language cues determine investigation thoroughness
4. **Implicit Requirements**: Agent infers unstated needs
5. **Conversational**: Feels like consulting a senior architect

## Example: Full Phase 4 Execution

### User Input
```bash
/prompt ideal-sti "Build a document collaboration platform for lawyers"
```

### Phase 4 Natural Language Generation
```markdown
By the time Phase 4 executes, the context has evolved:

Initial: "Build a document collaboration platform for lawyers"
After Phase 1: "+ needs audit trails, compliance, version control"  
After Phase 2: "+ goals: reduce review time 50%, maintain compliance"
After Phase 3: "+ constraints: on-premise option required, SOC2 needed"

Phase 4 receives: "Given a document collaboration platform for lawyers that needs 
audit trails, compliance, version control, with goals to reduce review time 50% 
while maintaining compliance, and constraints requiring on-premise option and SOC2, 
investigate the best technology approach"

The tech-investigator agent then:
1. Recognizes legal/compliance requirements
2. Investigates on-premise capable stacks
3. Finds GitHub examples of compliant document systems
4. Tests audit trail implementations
5. Recommends appropriate technology with evidence
```

## Prompt-as-Code Pattern

The key insight is that prompts become code through:

1. **Pattern Matching**: Natural language patterns trigger specific behaviors
2. **Context Accumulation**: Each phase adds to understanding
3. **Adaptive Execution**: Agent interprets based on accumulated context
4. **Evidence Requirements**: Stay strict regardless of casual language

```markdown
# This is "code" written in natural language
"If the user mentions 'real-time', investigate WebSocket vs SSE vs polling.
If they mention 'enterprise', check compliance and audit requirements.
If they say 'simple', prefer minimalist stacks over feature-rich ones.
Always provide GitHub evidence regardless of how the request is phrased."
```

## Summary

The v3 approach transforms IDEAL-STI from a command-line style system to a natural conversation where:

- Agents interpret intent from natural language
- Context builds organically through phases
- Technical requirements emerge from conversational exploration
- Evidence and rigor remain while feeling more intuitive

This makes the system feel less like executing commands and more like having a conversation with a senior technical advisor who happens to be extremely thorough about research and evidence.