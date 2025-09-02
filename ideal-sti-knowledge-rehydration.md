# IDEAL-STI Knowledge Rehydration & Quality Gates

## How IDEAL-STI Changed

The IDEAL-STI system has evolved to integrate knowledge rehydration and quality gates that determine when phases can operate with existing knowledge versus requiring fresh investigation.

### Key Changes from Original

#### 1. Knowledge Persistence Layer
```bash
# New directory structure for knowledge persistence
docs/planning/
├── .knowledge-state/
│   ├── tech-investigation-cache.json    # Cached tech-investigator results
│   ├── stakeholder-analysis.json        # Reusable stakeholder patterns
│   ├── market-research.json             # Market analysis cache
│   └── pattern-library.json             # Accumulated pattern knowledge
├── phase1-discovery.md
├── phase4-tech-investigation.md
└── ...
```

#### 2. Enhanced Phase Execution with Knowledge Checks
Each phase now checks for reusable knowledge before proceeding:

```bash
execute_phase_4() {
    local project_context="$1"
    echo "=== Phase 4: Technology Investigation ==="
    
    # NEW: Knowledge rehydration check
    if can_rehydrate_tech_knowledge "$project_context"; then
        echo "🔄 Rehydrating cached technology investigation..."
        rehydrate_tech_investigation "$project_context"
        return 0
    fi
    
    # NEW: Quality gate check before fresh investigation
    if ! has_sufficient_context_for_tech_investigation; then
        echo "❌ Insufficient context for technology investigation"
        echo "📝 Need: scale estimates, functional requirements, constraints"
        return 1
    fi
    
    # Proceed with fresh investigation
    launch_tech_investigation "$project_context"
}
```

#### 3. Outcome-Oriented Prompt Integration
```bash
# OLD: Command-style tech-investigator
tech-investigator --mode select --context "$arguments"

# NEW: Outcome-oriented prompts with natural language
local stack_context="Build a $project_type for $scale users with $key_features"

# Phase 4: Technology stack selection
stack-recommendation "$stack_context" --rehydrate-from ./docs/planning/.knowledge-state/stack-analysis.yaml

# Phase 4.5: Platform constraints investigation (if specific platform chosen)
platform-constraints "$chosen_platform scaling and limits" --focus-areas "execution,storage,cost"

# Migration planning (if replacing existing system)
migration-strategy "Migrate from $current_system to $target_system" --risk-tolerance conservative
```

## Knowledge Rehydration Mechanism

### 1. Knowledge Sources and Locations

The system scans for existing knowledge in this order:

```bash
# Priority 1: Current project knowledge
./docs/planning/.knowledge-state/

# Priority 2: Similar project patterns (via git history)
git log --grep="similar-to:$project_type" --format="%H:%s" | 
  head -5 | extract_project_references

# Priority 3: User knowledge folder
~/knowledge/tech-patterns/
~/knowledge/architecture-decisions/

# Priority 4: Organizational knowledge (if available)
../shared-knowledge/
$ORGANIZATION_KNOWLEDGE_PATH/

# Priority 5: Cached investigations (time-limited)
~/.ideal-sti-cache/investigations/
```

### 2. Knowledge Rehydration Functions

```bash
# Check if we can reuse existing technology investigation
can_rehydrate_tech_knowledge() {
    local project_context="$1"
    local project_hash=$(echo "$project_context" | sha256sum | cut -d' ' -f1)
    
    # Check for exact match (same project requirements)
    if [ -f "docs/planning/.knowledge-state/tech-investigation-${project_hash}.json" ]; then
        local cache_age=$(get_file_age "docs/planning/.knowledge-state/tech-investigation-${project_hash}.json")
        [ $cache_age -lt 2592000 ] && return 0  # 30 days
    fi
    
    # Check for similar project patterns
    local similar_projects=$(find_similar_projects "$project_context")
    if [ $(echo "$similar_projects" | wc -l) -ge 3 ]; then
        echo "🔍 Found similar project patterns"
        return 0
    fi
    
    return 1
}

# Rehydrate knowledge from cached sources
rehydrate_tech_investigation() {
    local project_context="$1"
    
    echo "📚 Rehydrating technology knowledge from:"
    
    # 1. Load exact cache if available
    local project_hash=$(echo "$project_context" | sha256sum | cut -d' ' -f1)
    local cache_file="docs/planning/.knowledge-state/tech-investigation-${project_hash}.json"
    
    if [ -f "$cache_file" ]; then
        echo "  - Exact cache match: $(basename $cache_file)"
        cat "$cache_file" > docs/planning/phase4-tech-investigation.md
        return 0
    fi
    
    # 2. Synthesize from similar projects
    local similar_investigations=$(find_similar_tech_investigations "$project_context")
    if [ -n "$similar_investigations" ]; then
        echo "  - Similar project synthesis"
        synthesize_tech_knowledge "$similar_investigations" > docs/planning/phase4-tech-investigation.md
        return 0
    fi
    
    # 3. Load organizational patterns
    if [ -d "$ORGANIZATION_KNOWLEDGE_PATH/tech-patterns" ]; then
        echo "  - Organizational knowledge patterns"
        apply_org_tech_patterns "$project_context" > docs/planning/phase4-tech-investigation.md
        return 0
    fi
    
    return 1
}

# Find projects with similar characteristics
find_similar_projects() {
    local project_context="$1"
    
    # Extract key characteristics
    local scale=$(echo "$project_context" | extract_scale)
    local type=$(echo "$project_context" | extract_project_type)
    local features=$(echo "$project_context" | extract_key_features)
    
    # Search knowledge sources
    grep -r "scale:$scale" ~/.ideal-sti-cache/investigations/ 2>/dev/null |
    grep "type:$type" |
    grep -E "$(echo $features | tr ' ' '|')" |
    cut -d: -f1 |
    head -5
}
```

### 3. Knowledge Synthesis

When rehydrating from multiple sources:

```bash
synthesize_tech_knowledge() {
    local investigation_files="$1"
    
    cat << EOF
# Phase 4: Technology Investigation (Synthesized)

Generated from similar project investigations:
$(echo "$investigation_files" | sed 's/^/- /')

## Recommended Stack (Pattern Analysis)

Based on analysis of $(echo "$investigation_files" | wc -l) similar projects:

$(extract_common_stack_recommendations "$investigation_files")

## Constraints from Similar Projects

$(extract_common_constraints "$investigation_files")

## Lessons from Production Usage

$(extract_production_lessons "$investigation_files")

**Note**: This investigation was rehydrated from similar projects. 
Run fresh investigation if requirements differ significantly.
EOF
}
```

## Quality Gates for Knowledge Sufficiency

### 1. Phase Entry Requirements

Each phase now has knowledge sufficiency checks:

```bash
# Phase 4: Technology Investigation Requirements
has_sufficient_context_for_tech_investigation() {
    local required_knowledge=(
        "scale-estimate"      # From Phase 1 or 2
        "project-type"        # From Phase 1
        "key-features"        # From Phase 1 or 2
        "constraints"         # From Phase 3 or existing knowledge
    )
    
    for requirement in "${required_knowledge[@]}"; do
        if ! has_knowledge "$requirement"; then
            echo "❌ Missing required knowledge: $requirement"
            return 1
        fi
    done
    
    return 0
}

# Generic knowledge checker
has_knowledge() {
    local knowledge_type="$1"
    
    case "$knowledge_type" in
        "scale-estimate")
            grep -q "users:\|scale:\|volume:" docs/planning/phase*-*.md 2>/dev/null
            ;;
        "project-type")
            grep -q "type:\|application:\|platform:" docs/planning/phase1-*.md 2>/dev/null
            ;;
        "key-features")
            grep -q "features:\|requirements:\|capabilities:" docs/planning/phase*-*.md 2>/dev/null
            ;;
        "constraints")
            grep -q "constraints:\|limitations:\|requirements:" docs/planning/phase*-*.md 2>/dev/null
            ;;
    esac
}
```

### 2. Knowledge Completeness Validation

```bash
validate_phase_knowledge_completeness() {
    local phase="$1"
    local phase_file="docs/planning/phase${phase}-*.md"
    
    case $phase in
        4) # Technology Investigation
            local required_sections=(
                "Recommended Stack"
                "GitHub Evidence"
                "Constraints"
                "Implementation Patterns"
            )
            
            # Check if this is rehydrated or fresh
            if grep -q "Synthesized" "$phase_file"; then
                echo "🔄 Validating rehydrated knowledge..."
                validate_rehydrated_tech_knowledge "$phase_file"
            else
                echo "🆕 Validating fresh investigation..."
                validate_fresh_tech_knowledge "$phase_file"
            fi
            ;;
    esac
}

validate_rehydrated_tech_knowledge() {
    local phase_file="$1"
    
    # Rehydrated knowledge has different validation criteria
    local required_for_rehydrated=(
        "Pattern Analysis"     # Must show synthesis method
        "Similar Projects"     # Must reference source projects
        "Production Lessons"   # Must include real-world learnings
    )
    
    for section in "${required_for_rehydrated[@]}"; do
        if ! grep -q "$section" "$phase_file"; then
            echo "❌ Rehydrated knowledge missing: $section"
            echo "💡 Recommendation: Run fresh investigation"
            return 1
        fi
    done
    
    # Check freshness of source investigations
    local source_age=$(extract_oldest_source_age "$phase_file")
    if [ $source_age -gt 5184000 ]; then  # 60 days
        echo "⚠️ Source investigations are old (>60 days)"
        echo "💡 Consider fresh investigation for current practices"
    fi
    
    return 0
}

validate_fresh_tech_knowledge() {
    local phase_file="$1"
    
    # Fresh investigations must meet full evidence requirements
    local github_repo_count=$(grep -c "github.com" "$phase_file")
    if [ $github_repo_count -lt 5 ]; then
        echo "❌ Insufficient GitHub evidence ($github_repo_count repos, need 5+)"
        return 1
    fi
    
    # Check for actual testing evidence
    if ! grep -q "test.*code\|empirical\|measured" "$phase_file"; then
        echo "❌ Missing empirical testing evidence"
        return 1
    fi
    
    return 0
}
```

### 3. Knowledge Gap Detection and Recovery

```bash
# Detect when we need to fall back to fresh investigation
detect_knowledge_gaps() {
    local project_context="$1"
    
    # Check for project uniqueness factors
    local unique_factors=$(detect_unique_project_factors "$project_context")
    
    if [ -n "$unique_factors" ]; then
        echo "🔍 Unique project factors detected:"
        echo "$unique_factors" | sed 's/^/  - /'
        echo "💡 Recommendation: Fresh investigation required"
        return 1
    fi
    
    # Check for technology evolution
    local tech_evolution=$(check_technology_evolution)
    if [ -n "$tech_evolution" ]; then
        echo "📈 Significant technology evolution detected:"
        echo "$tech_evolution" | sed 's/^/  - /'
        echo "💡 Recommendation: Update investigation"
        return 1
    fi
    
    return 0
}

detect_unique_project_factors() {
    local project_context="$1"
    
    # Factors that make projects unique
    local unique_patterns=(
        "real-time.*collaboration"
        "AI.*integration"
        "blockchain.*crypto"
        "regulatory.*compliance"
        "scientific.*computing"
    )
    
    for pattern in "${unique_patterns[@]}"; do
        if echo "$project_context" | grep -qi "$pattern"; then
            echo "Unique factor: $pattern"
        fi
    done
}
```

## Integration with Tech-Investigator

### New Invocation Pattern

```bash
# Phase 4 now builds rich context for tech-investigator
execute_phase_4() {
    local project_description="$1"
    
    # Build contextual prompt from accumulated knowledge
    local context=""
    context+="Project: $(extract_project_summary)"
    context+=". Scale: $(extract_scale_requirements)"
    context+=". Features: $(extract_key_features)"
    context+=". Constraints: $(extract_constraints)"
    
    # Quality gate: ensure sufficient context
    if ! validate_context_sufficiency "$context"; then
        echo "❌ Insufficient context for technology investigation"
        suggest_context_gathering
        return 1
    fi
    
    # Natural language tech investigation
    local investigation_prompt="Given this project context: $context, 
    investigate the best technology approach. Consider both proven and innovative options."
    
    # Check for rehydration possibility first
    if can_rehydrate_tech_knowledge "$context"; then
        echo "🔄 Found similar investigations - rehydrating knowledge"
        rehydrate_tech_investigation "$context"
    else
        echo "🆕 Launching fresh technology investigation"
        stack-recommendation "$investigation_prompt"
    fi
    
    # Cache results for future rehydration
    cache_tech_investigation "$context" "docs/planning/phase4-tech-investigation.md"
}
```

### Quality Gates Integration

The system now determines when to:

1. **Rehydrate** (use cached knowledge): When similar projects exist and tech hasn't evolved significantly
2. **Fresh Investigation** (new analysis): When project is unique or cached knowledge is stale
3. **Hybrid** (combine approaches): When partial knowledge exists but gaps remain

This creates an adaptive system that builds organizational knowledge over time while ensuring fresh analysis when needed.

## Benefits

1. **Speed**: Rehydration can complete Phase 4 in minutes vs hours
2. **Consistency**: Organizational patterns ensure consistent technology choices
3. **Learning**: System builds knowledge base over time
4. **Quality**: Gates prevent insufficient analysis
5. **Freshness**: Detects when cached knowledge is outdated