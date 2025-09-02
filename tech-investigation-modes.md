# Technology Investigation Modes - Prompt as Code

## Mode Selection Directive

When investigating technology for a project, first determine the investigation mode:

```markdown
DETERMINE INVESTIGATION MODE:
- If request contains "how to choose", "what stack", "compare", "evaluate" → STACK SELECTION MODE
- If request contains "how does X work", "implementation details", "deep dive" → DEEP INVESTIGATION MODE
- If unclear → Ask: "Are you selecting technologies or understanding a specific stack?"
```

## Mode 1: Architecture Stack Selection

### Purpose
Choose the optimal technology combination for a project - minimalist but sufficient.

### Execution Instructions

```markdown
STACK SELECTION ANALYSIS:

1. **Decision Dimensions** (Evaluate each independently)
   - Frontend Framework Selection
     * React vs Vue vs Svelte vs Angular vs Vanilla
     * Decision factors: Team expertise, ecosystem, performance needs, bundle size
   - Backend Architecture
     * Serverless vs Microservices vs Monolith
     * Runtime: Node vs Python vs Go vs Java
   - Storage Strategy
     * SQL vs NoSQL vs Multi-model
     * Managed vs Self-hosted
   - Infrastructure Pattern
     * Cloud-native vs Hybrid vs On-premise
     * Container vs Serverless vs Traditional

2. **Minimalist Sufficiency Check**
   For each technology option, answer:
   - What's the simplest option that meets requirements?
   - What capabilities would we lose with a simpler choice?
   - What complexity would we add with a richer choice?
   - Is the added complexity justified by clear needs?

3. **Stack Compatibility Matrix**
   Create compatibility scoring:
   | Frontend | Backend | Database | Score | Reasoning |
   |----------|---------|----------|-------|-----------|
   | React    | Node    | Postgres | 9/10  | Same language, mature ecosystem |
   | Vue      | Python  | MongoDB  | 7/10  | Different paradigms but proven |
   | Svelte   | Go      | SQLite   | 8/10  | Performance-focused, simple |

4. **Decision Factors Analysis**
   - Development velocity vs Performance
   - Initial cost vs Long-term maintenance
   - Team familiarity vs Industry trends
   - Feature richness vs Simplicity
   - Community support vs Enterprise backing

5. **GitHub Evidence Collection**
   For each candidate stack combination:
   - Find 3+ production apps using this exact combination
   - Extract lessons from their issues/discussions
   - Note migration patterns (what they moved FROM and TO)

6. **Output: Stack Recommendation**
   PRIMARY RECOMMENDATION:
   - Stack: [Frontend] + [Backend] + [Database] + [Infrastructure]
   - Justification: Why this combination optimizes for your constraints
   - Trade-offs accepted: What you're giving up
   - Migration path: How to evolve if needs change

   ALTERNATIVE STACKS:
   - Conservative choice: Boring but bulletproof
   - Innovation choice: Modern with calculated risks
   - Enterprise choice: Scalable but complex
```

## Mode 2: Deep Stack Investigation

### Purpose
Understand the intricate implementation details of a specific technology stack.

### Execution Instructions

```markdown
DEEP STACK INVESTIGATION:

1. **Execution Model Investigation**
   For the target technology (e.g., Google Apps Script):
   - How is code actually executed? (V8 engine, sandboxed, quotas)
   - What's the request lifecycle? (cold starts, warm instances, timeouts)
   - Memory model and limitations? (heap size, execution time)
   - Concurrency model? (single-threaded, async patterns, locks)

2. **UI Framework Deep Dive** (if applicable)
   - Component lifecycle hooks and their timing
   - State management patterns and pitfalls
   - Rendering pipeline (virtual DOM, reactivity, hydration)
   - Event handling and propagation rules
   - Performance bottlenecks and optimization points

3. **Client-Server Communication**
   - Protocol details (REST, GraphQL, WebSocket, RPC)
   - Serialization boundaries and limitations
   - Authentication token flow (where stored, how refreshed)
   - CORS and security policies
   - Rate limiting and quota management

4. **Storage Layer Mechanics**
   - Transaction boundaries and isolation levels
   - Index strategies and query optimization
   - Data consistency models (eventual, strong, causal)
   - Backup and recovery mechanisms
   - Migration and schema evolution patterns

5. **Threading and State Management**
   - Process model (threads, workers, actors)
   - Shared state mechanisms (locks, channels, atoms)
   - Session management (where stored, how synchronized)
   - Cache layers and invalidation strategies
   - Background job processing patterns

6. **Authentication Deep Dive**
   - Token generation and validation pipeline
   - Session storage (cookies, localStorage, server-side)
   - OAuth flow implementation details
   - Permission and role evaluation timing
   - Security headers and CSRF protection

7. **Platform-Specific Constraints**
   Discover the "unwritten rules":
   - Undocumented quotas or limits
   - Performance cliffs (when things suddenly slow down)
   - Hidden dependencies or requirements
   - Platform-specific gotchas from production
   - Workarounds the community has developed

8. **Code Archaeology**
   For the specific platform:
   - Clone and analyze 3+ mature open-source projects
   - Extract patterns they ALL use (indicating requirements)
   - Find patterns they ALL avoid (indicating problems)
   - Document initialization sequences and boilerplate
   - Map the "happy path" vs edge cases

9. **Output: Implementation Blueprint**
   EXECUTION MODEL:
   - Detailed lifecycle diagram with timing
   - Code examples for each phase
   - Performance characteristics and limits

   CRITICAL PATTERNS:
   - Must-use patterns with explanations
   - Anti-patterns that cause failures
   - Platform-specific idioms

   IMPLEMENTATION CHECKLIST:
   - [ ] Environment setup requirements
   - [ ] Initialization sequence
   - [ ] State management approach
   - [ ] Error handling strategy
   - [ ] Testing approach specific to platform
   - [ ] Deployment pipeline requirements
```

## Mode Switching Protocol

```markdown
WHEN TO SWITCH MODES:

From Selection → Investigation:
- After choosing a stack, automatically trigger deep investigation
- User asks "now how do I implement this?"
- Encountering platform-specific issues during development

From Investigation → Selection:
- Deep investigation reveals stack is inappropriate
- Requirements change necessitating re-evaluation
- Performance issues require architecture change

HYBRID MODE:
When both are needed simultaneously:
1. Start with Selection (narrow to 2-3 candidates)
2. Deep investigate top candidates (2-4 hours each)
3. Return to Selection with investigation insights
4. Make final decision based on deep knowledge
```

## Prompt-as-Code Execution Pattern

```markdown
EXECUTION TEMPLATE:

User: "I need to build a real-time collaborative editor"