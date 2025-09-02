# IDEAL-STI Integration with Tech-Investigator Agent

## Phase Integration Points

### Phase 3: Feasibility Assessment
**Current**: Basic technical feasibility check  
**Enhanced**: Invoke tech-investigator for early constraint discovery

```bash
# In Phase 3 execution
execute_phase_3() {
    local arguments="$1"
    echo "=== Phase 3: Feasibility Assessment ==="
    
    # NEW: Early technology constraint discovery
    echo "🔬 Launching tech-investigator for constraint discovery..."
    
    # If platform is mentioned in requirements, deep investigate
    if echo "$arguments" | grep -qi "google\|salesforce\|aws\|azure"; then
        /prompt tech-investigator --mode deep \
          --stack "$(extract_platform $arguments)" \
          --focus "execution-model scaling cost" \
          > docs/planning/phase3-platform-constraints.md
    fi
    
    # Continue normal Phase 3...
}
```

### Phase 4: Technology Research (COMPLETE REPLACEMENT)

```bash
# Replace entire Phase 4 with tech-investigator
execute_phase_4() {
    local arguments="$1"
    echo "=== Phase 4: Technology Research & Investigation ==="
    
    # Determine investigation mode based on project type
    local mode="select"  # default
    local existing_stack=""
    
    # Check if Phase 0 found existing technology
    if [ -f "docs/planning/phase0-existing-analysis.md" ]; then
        existing_stack=$(grep "Project Type:" docs/planning/phase0-existing-analysis.md | cut -d: -f2)
        if [ -n "$existing_stack" ]; then
            mode="hybrid"  # Investigate current + alternatives
        fi
    fi
    
    # Check if Phase 3 identified platform constraints
    if grep -q "Platform Requirement:" docs/planning/phase3-feasibility.md 2>/dev/null; then
        mode="deep"  # Deep investigate required platform
        existing_stack=$(grep "Platform Requirement:" docs/planning/phase3-feasibility.md | cut -d: -f2)
    fi
    
    # Launch tech-investigator with appropriate mode
    case $mode in
        select)
            echo "📊 Mode: Technology Selection (greenfield)"
            /prompt tech-investigator --mode select \
              --context "$arguments" \
              > docs/planning/phase4-tech-selection.md
            ;;
            
        deep)
            echo "🔍 Mode: Deep Platform Investigation (constrained)"
            /prompt tech-investigator --mode deep \
              --stack "$existing_stack" \
              --focus "all" \
              > docs/planning/phase4-platform-investigation.md
            ;;
            
        hybrid)
            echo "🔄 Mode: Hybrid Investigation (migration/enhancement)"
            /prompt tech-investigator --mode hybrid \
              --context "$arguments" \
              --depth 3 \
              > docs/planning/phase4-hybrid-investigation.md
            ;;
    esac
    
    # Validate tech-investigator output
    validate_tech_investigation "docs/planning/phase4-*.md"
}

validate_tech_investigation() {
    local files="$1"
    
    # Check for required sections based on mode
    if ls $files | grep -q "selection"; then
        # Validate selection mode output
        for required in "primary_stack" "alternatives" "github_repos" "decision_matrix"; do
            if ! grep -q "$required" $files; then
                echo "❌ Missing required section: $required"
                return 1
            fi
        done
    fi
    
    if ls $files | grep -q "investigation"; then
        # Validate deep mode output
        for required in "constraints" "patterns" "anti_patterns" "boilerplate"; do
            if ! grep -q "$required" $files; then
                echo "❌ Missing required section: $required"
                return 1
            fi
        done
    fi
    
    echo "✅ Technology investigation validated"
    return 0
}
```

### Phase 5: Requirements Definition
**Enhancement**: Use discovered constraints to shape requirements

```bash
execute_phase_5() {
    local arguments="$1"
    echo "=== Phase 5: Requirements Definition ==="
    
    # Import platform constraints from tech-investigator
    if [ -f "docs/planning/phase4-platform-investigation.md" ]; then
        echo "📥 Importing platform constraints into requirements..."
        
        # Extract constraints and add to requirements
        local constraints=$(grep -A 20 "MUST-KNOW CONSTRAINTS" docs/planning/phase4-*.md)
        
        # Add platform-specific requirements
        cat >> docs/planning/phase5-requirements.md << EOF

## Platform-Specific Requirements (from tech-investigator)

### Non-Functional Requirements (Platform Constraints)
$constraints

### Adjusted Functional Requirements
- All real-time features must work within [discovered latency]
- Batch operations must complete within [execution limit]
- File uploads cannot exceed [platform file size limit]
EOF
    fi
    
    # Continue normal Phase 5...
}
```

### Phase 7: Architecture Design
**Enhancement**: Use investigation patterns for architecture

```bash
execute_phase_7() {
    local arguments="$1"
    echo "=== Phase 7: Architecture Design ==="
    
    # Import architecture patterns from tech-investigator
    if [ -f "docs/planning/phase4-*-investigation.md" ]; then
        echo "📐 Applying discovered patterns to architecture..."
        
        # Extract required patterns
        local patterns=$(grep -A 30 "REQUIRED PATTERNS" docs/planning/phase4-*.md)
        local antipatterns=$(grep -A 20 "ANTI-PATTERNS" docs/planning/phase4-*.md)
        
        # Generate architecture with platform awareness
        cat >> docs/planning/phase7-architecture.md << EOF

## Platform-Informed Architecture

### Required Architectural Patterns
$patterns

### Architectural Anti-Patterns to Avoid
$antipatterns

### Platform-Specific Components
$(extract_boilerplate_architecture)
EOF
    fi
    
    # Continue normal Phase 7...
}
```

### Phase 8: Decision Registry
**Enhancement**: Document investigation-based decisions

```bash
execute_phase_8() {
    echo "=== Phase 8: Decision Registry ==="
    
    # Auto-document technology decisions from tech-investigator
    cat >> docs/planning/phase8-decisions.md << EOF

## Technology Stack Decision

**Decision ID**: TECH-001
**Date**: $(date -I)
**Source**: tech-investigator analysis

### Decision
$(grep "primary_stack:" docs/planning/phase4-*.md | head -1)

### Rationale
$(grep "rationale:" docs/planning/phase4-*.md | head -1)

### Alternatives Considered
$(grep -A 10 "alternatives:" docs/planning/phase4-*.md)

### Trade-offs Accepted
- $(grep "trade.*off" docs/planning/phase4-*.md | head -5)

### Reversal Triggers
- If scale exceeds [limit] users
- If latency requirements change to <[threshold]ms
- If platform changes pricing model significantly
EOF
}
```

### Phase 10: Deployment Strategy
**Enhancement**: Use platform-specific deployment patterns

```bash
execute_phase_10() {
    echo "=== Phase 10: Deployment & Operations ==="
    
    # Import deployment patterns from deep investigation
    if grep -q "deployment" docs/planning/phase4-*-investigation.md 2>/dev/null; then
        local deployment_guide=$(grep -A 50 "DEPLOYMENT" docs/planning/phase4-*.md)
        
        cat >> docs/planning/phase10-deployment.md << EOF

## Platform-Specific Deployment

### Deployment Pipeline (from tech-investigator)
$deployment_guide

### Monitoring Strategy
$(grep -A 20 "monitoring" docs/planning/phase4-*.md)

### Scaling Triggers
$(grep -A 10 "scaling" docs/planning/phase4-*.md)
EOF
    fi
}
```

## Parallel Execution with Worktrees

The tech-investigator agent can utilize worktree isolation for parallel investigation:

```bash
# Enhanced launch_tech_investigation function
launch_tech_investigation() {
    local mode="$1"
    local context="$2"
    local depth="${3:-2}"
    
    case $mode in
        hybrid)
            # Run selection first
            /prompt tech-investigator --mode select --context "$context" \
              > docs/planning/phase4-selection.md
            
            # Extract top N candidates
            local candidates=$(grep "primary\|alternative" docs/planning/phase4-selection.md | \
              head -$depth | cut -d: -f2)
            
            # Launch parallel deep investigations
            for candidate in $candidates; do
                echo "🔬 Deep investigating: $candidate"
                
                # Create isolated worktree for investigation
                create_isolated_worktree "tech-inv-${candidate}" "investigation"
                
                # Create investigation mission
                cat > "$CURRENT_WORKTREE/mission.md" << EOF
# Deep Investigation Mission: $candidate

Run comprehensive investigation of $candidate stack including:
- Execution model testing
- State management patterns
- Authentication flow
- Deployment patterns
- Common gotchas

Output to: investigation-${candidate}.md
EOF
                
                # Launch investigation in worktree
                (cd "$CURRENT_WORKTREE" && \
                  /prompt tech-investigator --mode deep \
                    --stack "$candidate" --focus "all" \
                    > investigation-${candidate}.md)
                
                # Cleanup merges results back
                cleanup_isolated_worktree
            done
            
            # Synthesize all investigations
            synthesize_investigations
            ;;
    esac
}

synthesize_investigations() {
    echo "📊 Synthesizing parallel investigations..."
    
    cat > docs/planning/phase4-synthesis.md << EOF
# Technology Investigation Synthesis

## Investigations Completed
$(ls docs/planning/investigation-*.md 2>/dev/null | wc -l) stacks investigated in parallel

## Comparison Matrix
$(generate_comparison_matrix)

## Final Recommendation
Based on deep investigation, the optimal stack is:
$(determine_winner_from_investigations)

## Implementation Guide
See: docs/planning/phase4-implementation-guide.md
EOF
}
```

## Invocation Examples

### From IDEAL-STI Phase 4
```bash
# Automatic mode selection based on project context
execute_phase_4 "Build a real-time collaborative editor for 1000 users"
```

### Direct Invocation
```bash
# Investigate specific platform deeply
/prompt tech-investigator --mode deep --stack "vercel-nextjs" --focus "edge-functions streaming"

# Select stack for new project
/prompt tech-investigator --mode select --context "e-commerce with 10k products, 500 orders/day"

# Hybrid investigation for migration
/prompt tech-investigator --mode hybrid --context "migrate from wordpress to modern stack" --depth 3
```

### From Other Phases
```bash
# Phase 3: Check platform feasibility
/prompt tech-investigator --mode deep --stack "salesforce" --focus "execution-model cost"

# Phase 7: Get architecture patterns
/prompt tech-investigator --mode deep --stack "selected-stack" --focus "patterns"

# Phase 10: Get deployment guide
/prompt tech-investigator --mode deep --stack "selected-stack" --focus "deployment monitoring"
```

## Benefits of Integration

1. **Adaptive Investigation**: Mode automatically selected based on project type
2. **Parallel Execution**: Worktree isolation for concurrent deep dives
3. **Evidence-Based**: All decisions backed by GitHub analysis and testing
4. **Phase Flow**: Investigation results flow naturally through phases
5. **Reusable**: Agent can be invoked independently or within IDEAL-STI

## Validation Requirements

The tech-investigator must provide:

**For Phase 4 Success**:
- ✓ Primary stack recommendation with rationale
- ✓ At least 2 alternatives considered
- ✓ 15+ GitHub repositories analyzed
- ✓ Platform constraints documented
- ✓ Implementation patterns provided

**For Phase 5-10 Integration**:
- ✓ Constraints feed into requirements
- ✓ Patterns inform architecture
- ✓ Decisions are documented
- ✓ Deployment guide included