# project-blueprint

**Produces**: Complete project implementation plan with phases, tasks, and dependencies  
**Solves**: "I need a comprehensive plan to build this project from start to deployment"

## Input Contract

### Required Input
- `<prompt-context>`: Project description with goals and constraints
- Example: "Build a SaaS project management tool for remote teams with real-time collaboration"

### Optional Input
- `--rehydrate-from <path>`: Path to previous similar project analysis
- `--constraints <list>`: Project constraints (timeline, budget, team size)
- `--complexity-level <level>`: simple|standard|complex

## Output Contract

```yaml
deliverable_type: project_blueprint
generated_at: [timestamp]
project_scope:
  project_name: [descriptive name]
  project_type: [web app, mobile app, API, platform, etc.]
  primary_goals: [main objectives]
  success_metrics: [measurable outcomes]
  target_users: [user personas and scale]
  
execution_timeline:
  total_duration: [weeks/months]
  team_size_estimate: [number of people needed]
  complexity_assessment: [simple|moderate|complex]
  confidence_level: [high|medium|low in timeline estimate]

phases:
  phase_0:
    name: "Project Foundation"
    duration: [weeks]
    objectives: [what this phase accomplishes]
    deliverables:
      - Project setup and tooling
      - Development environment
      - CI/CD pipeline foundation
      - Team onboarding materials
    success_criteria:
      - Development environment fully functional
      - Team can deploy hello-world to production
      - Documentation structure established
    
  phase_1:
    name: "Core Infrastructure" 
    duration: [weeks]
    objectives: [authentication, database, core APIs]
    deliverables:
      - User authentication system
      - Database schema and migrations
      - Core API endpoints
      - Basic security implementation
    dependencies:
      - Phase 0 complete
      - Technology stack finalized
    success_criteria:
      - Users can register and authenticate
      - Core data models functional
      - API endpoints tested and documented
    
  phase_2:
    name: "MVP Features"
    duration: [weeks] 
    objectives: [minimum viable product functionality]
    deliverables:
      - Core user workflows
      - Basic UI/UX implementation
      - Essential business logic
      - Integration with external services
    dependencies:
      - Phase 1 complete
      - UI/UX designs approved
    success_criteria:
      - End-to-end user workflows functional
      - MVP ready for internal testing
      - Performance meets basic requirements
    
  phase_3:
    name: "Polish & Launch Prep"
    duration: [weeks]
    objectives: [production readiness and user experience]
    deliverables:
      - UI/UX refinements
      - Performance optimizations  
      - Monitoring and analytics
      - Production deployment
    dependencies:
      - Phase 2 complete
      - Load testing completed
    success_criteria:
      - Production deployment stable
      - Monitoring systems operational
      - Ready for user onboarding

technology_recommendations:
  rehydrated_from: [path if using previous analysis]
  primary_stack:
    frontend: [technology and rationale]
    backend: [technology and rationale] 
    database: [technology and rationale]
    infrastructure: [deployment platform and rationale]
  
  development_tools:
    version_control: [Git workflow and branching strategy]
    ci_cd: [continuous integration and deployment approach]
    testing: [testing framework and strategy]
    monitoring: [observability and error tracking]

architecture_overview:
  system_components:
    - component: [name]
      responsibility: [what it does]
      technology: [implementation technology]
      interactions: [how it connects to other components]
  
  data_flow:
    user_request_path: [how requests are processed]
    data_persistence: [how data is stored and retrieved]
    external_integrations: [third-party service interactions]
  
  security_approach:
    authentication: [how users are authenticated]
    authorization: [how permissions are managed]
    data_protection: [how sensitive data is secured]
    api_security: [how APIs are protected]

detailed_tasks:
  phase_0_tasks:
    - task: "Set up development environment"
      estimate: [hours/days]
      assignee_type: [developer role]
      dependencies: [prerequisite tasks]
      acceptance_criteria: [specific completion requirements]
    
    - task: "Configure CI/CD pipeline"
      estimate: [hours/days] 
      assignee_type: [DevOps or senior developer]
      dependencies: [repository setup, cloud account]
      acceptance_criteria: [automated deployment working]
  
  phase_1_tasks:
    - task: "Implement user registration"
      estimate: [hours/days]
      assignee_type: [backend developer]
      dependencies: [database setup, authentication design]
      acceptance_criteria: [users can register via API]
    
    # ... additional tasks for each phase

risk_assessment:
  technical_risks:
    - risk: [specific technical challenge]
      probability: [low|medium|high]
      impact: [low|medium|high]
      mitigation: [how to reduce or handle risk]
      contingency: [alternative approach if risk materializes]
  
  project_risks:
    - risk: [timeline, budget, or team risk]
      probability: [low|medium|high]
      impact: [low|medium|high] 
      mitigation: [risk reduction strategy]
      early_warning_signs: [indicators risk is materializing]

team_recommendations:
  ideal_team_composition:
    - role: "Tech Lead/Senior Developer"
      count: 1
      responsibilities: [architecture decisions, code review]
      required_skills: [specific technologies and experience]
    
    - role: "Frontend Developer"  
      count: [1-2]
      responsibilities: [UI implementation, user experience]
      required_skills: [frontend technologies, design skills]
    
    - role: "Backend Developer"
      count: [1-2] 
      responsibilities: [API development, database design]
      required_skills: [backend technologies, system design]
  
  skill_development_plan:
    critical_skills: [skills team must have]
    learning_timeline: [when skills must be acquired]
    training_approach: [how team will learn new technologies]

testing_strategy:
  testing_pyramid:
    unit_tests: [coverage goals and approach]
    integration_tests: [what to test and how]
    end_to_end_tests: [critical user journeys]
  
  quality_assurance:
    code_review_process: [how code is reviewed]
    automated_testing: [CI/CD integration]
    manual_testing: [what requires human testing]
    performance_testing: [load testing approach]

deployment_strategy:
  environments:
    development: [local development setup]
    staging: [pre-production environment]
    production: [live user environment]
  
  deployment_approach:
    method: [blue-green, rolling, canary]
    automation_level: [fully automated vs manual steps]
    rollback_plan: [how to undo deployments]
    monitoring: [how to detect deployment issues]

success_metrics:
  development_metrics:
    - metric: [code coverage, deployment frequency]
      target: [specific goal]
      measurement: [how to track]
  
  business_metrics:
    - metric: [user adoption, performance, availability]
      baseline: [starting point]
      target: [desired outcome]
      measurement: [tracking method]

budget_estimation:
  development_costs:
    labor: [person-months × average rate]
    infrastructure: [cloud costs, services, tools]
    third_party_services: [APIs, integrations, licenses]
  
  ongoing_costs:
    hosting: [monthly infrastructure costs]
    maintenance: [ongoing development effort]
    support: [customer support requirements]

lessons_from_similar_projects:
  github_evidence:
    - project: [similar open source project]
      technology_stack: [what they used]
      architecture_lessons: [patterns they used]
      pitfalls_avoided: [what they learned not to do]
  
  industry_patterns:
    common_approaches: [how similar projects are typically built]
    emerging_trends: [new patterns gaining traction]
    anti_patterns: [approaches to avoid]

next_actions:
  immediate: [tasks for next 1-2 weeks]
  short_term: [activities for next month]
  decision_points: [key decisions needed before proceeding]
  preparation_tasks: [work needed before development begins]
```

## Execution Protocol

### Step 1: Project Scope Analysis
Parse the `<prompt-context>` to understand:
- Project type and primary use case
- Target users and scale requirements
- Key features and functionality
- Success metrics and business goals
- Constraints (timeline, budget, team, technical)

If `--rehydrate-from` provided:
- Load previous project analysis
- Identify reusable components and patterns
- Adapt timeline and approach based on similarities

### Step 2: Technology Foundation
Either reuse from rehydration source or invoke supporting prompts:
- Technology stack recommendation (via stack-recommendation prompt)
- Platform constraints analysis (via platform-constraints prompt)
- Architecture patterns from similar projects

### Step 3: Phase Definition
Structure project into logical phases:

**Phase 0**: Foundation (tooling, environment, CI/CD)
**Phase 1**: Core Infrastructure (auth, database, APIs)  
**Phase 2**: MVP Features (core user workflows)
**Phase 3**: Polish & Launch (performance, monitoring, production)
**Phase N+**: Post-launch iterations

Each phase includes:
- Clear objectives and deliverables
- Success criteria for completion
- Dependencies on previous phases
- Risk assessment and mitigation

### Step 4: Task Breakdown
For each phase, create detailed tasks:
- Specific, actionable work items
- Effort estimates in hours/days
- Skill requirements and assignee types
- Dependencies and acceptance criteria
- Testing and validation approaches

### Step 5: Risk Assessment
Identify project risks across categories:

**Technical Risks**: Integration complexity, performance challenges, technology learning curves
**Project Risks**: Timeline pressures, team availability, requirement changes
**Business Risks**: Market timing, user adoption, competitive threats

For each risk, define:
- Probability and impact assessment
- Mitigation strategies to reduce likelihood
- Contingency plans if risks materialize
- Early warning indicators

### Step 6: Team and Resource Planning
Recommend ideal team composition:
- Required roles and responsibilities
- Skill requirements for each role
- Training needs for new technologies
- Team scaling throughout project phases

### Step 7: Quality and Deployment Strategy
Define approaches for:
- Testing strategy (unit, integration, E2E)
- Code quality and review processes
- Deployment automation and environments
- Monitoring and observability

### Step 8: Evidence-Based Validation
Research similar projects for lessons learned:
- GitHub repositories with similar scope
- Blog posts about project implementations
- Case studies and post-mortems
- Industry best practices and anti-patterns

## Quality Gates

Before returning results, validate:
- ✅ Clear phase breakdown with dependencies
- ✅ Detailed tasks with effort estimates
- ✅ Risk assessment with mitigation strategies
- ✅ Team composition recommendations
- ✅ Technology choices justified with evidence
- ✅ Success metrics defined and measurable
- ✅ Budget and timeline estimates provided

## Example Usage

### Basic Project Planning
```
Ask prompter to run project-blueprint with context: "Build a collaborative document editor for remote teams with real-time sync and version history"
```

### With Constraints
```
Ask prompter to run project-blueprint with context: "Create a mobile expense tracking app" and constraints: "6 month timeline, 2 person team, iOS first"
```

### With Rehydration
```
Ask prompter to run project-blueprint with context: "Build a task management SaaS" and rehydration from ./previous-analysis/project-management-app.yaml
```

## Integration with Other Prompts

This prompt orchestrates other prompts:
- `stack-recommendation`: For technology decisions (Phase 1)
- `platform-constraints`: For implementation guidance (Phase 2)
- `architecture-design`: For system design (Phase 1)
- `migration-strategy`: If replacing existing system

Output becomes input for:
- Task management systems (import detailed tasks)
- Project planning tools (timeline and dependencies)
- Team planning (skill requirements and roles)

## Caching and Reuse

Results cached based on:
- Project type and complexity similarity
- Technology stack overlap
- Team size and timeline similarity

Cache expiration:
- 60 days for stable project types
- 30 days for emerging project patterns
- Invalidated when major technology ecosystem changes occur