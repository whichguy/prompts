# IDEAL-STI Integration with Outcome-Oriented Prompts

## Phase Integration Points

### Phase 3: Feasibility Assessment
**Current**: Basic technical feasibility check  
**Enhanced**: Invoke outcome-oriented prompts for early constraint discovery

```bash
# In Phase 3 execution
execute_phase_3() {
    local arguments="$1"
    echo "=== Phase 3: Feasibility Assessment ==="
    
    # NEW: Early platform constraint discovery with natural language
    echo "🔬 Launching platform constraints analysis for early feasibility..."
    
    # If platform is mentioned in requirements, investigate constraints
    if echo "$arguments" | grep -qi "google\|salesforce\|aws\|azure"; then
        local platform=$(extract_platform "$arguments")
        platform-constraints "$platform execution limits and scaling constraints for $arguments" \
          --focus-areas "execution,scaling,cost" \
          > docs/planning/phase3-platform-constraints.yaml
    fi
    
    # Continue normal Phase 3...
}
```

### Phase 4: Technology Research (COMPLETE REPLACEMENT)

```bash
# Replace entire Phase 4 with outcome-oriented prompts
execute_phase_4() {
    local arguments="$1"
    echo "=== Phase 4: Technology Research & Investigation ==="
    
    # Check for rehydration opportunities
    local rehydration_path=""
    if [ -f "docs/planning/.knowledge-state/stack-analysis.yaml" ]; then
        rehydration_path="--rehydrate-from docs/planning/.knowledge-state/stack-analysis.yaml"
    fi
    
    # Determine approach based on project context
    local existing_stack=""
    local approach="greenfield"  # default
    
    # Check if Phase 0 found existing technology
    if [ -f "docs/planning/phase0-existing-analysis.md" ]; then
        existing_stack=$(grep "Project Type:" docs/planning/phase0-existing-analysis.md | cut -d: -f2)
        if [ -n "$existing_stack" ]; then
            approach="migration"  # Migration from existing system
        fi
    fi
    
    # Check if Phase 3 identified platform constraints
    if grep -q "Platform Requirement:" docs/planning/phase3-feasibility.md 2>/dev/null; then
        approach="constrained"  # Specific platform required
        existing_stack=$(grep "Platform Requirement:" docs/planning/phase3-feasibility.md | cut -d: -f2)
    fi
    
    # Launch appropriate outcome-oriented prompts
    case $approach in
        greenfield)
            echo "📊 Approach: Technology Stack Selection (new project)"
            stack-recommendation "$arguments" $rehydration_path \
              > docs/planning/phase4-stack-recommendation.yaml
            ;;
            
        constrained)
            echo "🔍 Approach: Platform Constraints Investigation (specific platform)"
            platform-constraints "$existing_stack for project: $arguments" \
              --focus-areas "execution,storage,scaling,integration" \
              > docs/planning/phase4-platform-constraints.yaml
            ;;
            
        migration)
            echo "🔄 Approach: Migration Strategy (modernizing existing system)"
            migration-strategy "Migrate from $existing_stack for project: $arguments" \
              --risk-tolerance conservative $rehydration_path \
              > docs/planning/phase4-migration-strategy.yaml
            ;;
    esac
    
    # Always get complete project blueprint incorporating technology decisions
    echo "📋 Generating comprehensive project blueprint..."
    project-blueprint "$arguments" $rehydration_path \
      > docs/planning/phase4-project-blueprint.yaml
    
    # Validate outcome-oriented prompt outputs
    validate_phase4_outputs "docs/planning/phase4-*.yaml"
}

validate_phase4_outputs() {
    local files="$1"
    
    echo "🔍 Validating Phase 4 outcome-oriented prompt outputs..."
    
    # Check for YAML structure and required fields
    for file in $files; do
        if [ ! -f "$file" ]; then
            echo "❌ Missing expected output file: $file"
            return 1
        fi
        
        # Validate YAML structure
        if ! python3 -c "import yaml; yaml.safe_load(open('$file'))" 2>/dev/null; then
            echo "❌ Invalid YAML structure in: $file"
            return 1
        fi
        
        # Check for deliverable_type field
        if ! grep -q "deliverable_type:" "$file"; then
            echo "❌ Missing deliverable_type in: $file"
            return 1
        fi
        
        # Check for confidence_level field
        if ! grep -q "confidence_level:" "$file"; then
            echo "❌ Missing confidence_level in: $file"
            return 1
        fi
    done
    
    # Validate specific output types
    if ls $files | grep -q "stack-recommendation"; then
        # Validate stack recommendation output
        for required in "primary_stack" "alternatives" "github_evidence" "compatibility_analysis"; do
            if ! grep -q "$required" $files; then
                echo "❌ Missing required section: $required"
                return 1
            fi
        done
    fi
    
    if ls $files | grep -q "platform-constraints"; then
        # Validate platform constraints output  
        for required in "execution_model" "discovered_quotas" "production_patterns" "limitations_for_use_cases"; do
            if ! grep -q "$required" $files; then
                echo "❌ Missing required section: $required"
                return 1
            fi
        done
    fi
    
    if ls $files | grep -q "migration-strategy"; then
        # Validate migration strategy output
        for required in "migration_approach" "zero_downtime_strategy" "risk_assessment" "implementation_roadmap"; do
            if ! grep -q "$required" $files; then
                echo "❌ Missing required section: $required"
                return 1
            fi
        done
    fi
    
    if ls $files | grep -q "project-blueprint"; then
        # Validate project blueprint output
        for required in "phases" "team_recommendations" "risk_assessment" "success_metrics"; do
            if ! grep -q "$required" $files; then
                echo "❌ Missing required section: $required"
                return 1
            fi
        done
    fi
    
    echo "✅ Phase 4 outcome-oriented prompts validated successfully"
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

The outcome-oriented prompts can utilize worktree isolation for parallel investigation:

```bash
# Enhanced launch_technology_investigation function
launch_technology_investigation() {
    local approach="$1"
    local context="$2"
    local depth="${3:-2}"
    
    case $approach in
        comparative_analysis)
            # Run stack recommendation first to get alternatives
            stack-recommendation "$context" > docs/planning/phase4-stack-analysis.yaml
            
            # Extract primary and alternative stacks
            local stacks=$(grep -A 10 "alternatives:" docs/planning/phase4-stack-analysis.yaml | \
              grep "stack:" | cut -d: -f2 | head -$depth)
            
            # Launch parallel platform constraint investigations
            for stack in $stacks; do
                echo "🔬 Investigating platform constraints: $stack"
                
                # Create isolated worktree for investigation
                create_isolated_worktree "constraints-${stack}" "investigation"
                
                # Create investigation mission
                cat > "$CURRENT_WORKTREE/mission.md" << EOF
# Platform Constraints Investigation: $stack

Analyze platform limitations and implementation patterns for:
- Execution model and performance
- State management and persistence
- Authentication and security
- Deployment and monitoring
- Production patterns and gotchas

Output to: constraints-${stack}.yaml
EOF
                
                # Launch platform constraints analysis in worktree
                (cd "$CURRENT_WORKTREE" && \
                  platform-constraints "$stack platform analysis for: $context" \
                    --focus-areas "execution,storage,scaling,deployment" \
                    > constraints-${stack}.yaml)
                
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
platform-constraints "Vercel Edge Functions and Next.js" --focus-areas "edge-functions,streaming,performance"

# Select stack for new project  
stack-recommendation "e-commerce platform with 10k products, 500 orders/day"

# Migration planning for existing system
migration-strategy "migrate from WordPress to modern stack with 3 alternative approaches"
```

### From Other Phases
```bash
# Phase 3: Check platform feasibility
platform-constraints "Salesforce platform" --focus-areas "execution-model,cost,limitations"

# Phase 7: Get architecture patterns
platform-constraints "selected technology stack" --focus-areas "patterns,architecture,best-practices"

# Phase 10: Get deployment guide
platform-constraints "selected technology stack" --focus-areas "deployment,monitoring,production"
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