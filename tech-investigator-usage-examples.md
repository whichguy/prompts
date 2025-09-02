# Tech-Investigator - Natural Language Usage Examples

## Example 1: Simple Stack Selection Request

### User Prompt
```bash
/prompt tech-investigator "I need to build a recipe sharing website where users can post recipes with photos"
```

### Agent Interpretation & Execution
```markdown
INTERPRETING: "recipe sharing website where users can post recipes with photos"

DETECTED INTENT: Stack Selection
- Content sharing platform
- User-generated content
- Image uploads required
- Social features implied

INVESTIGATING TECHNOLOGY OPTIONS...

Based on your recipe sharing platform needs, I've evaluated several approaches:

**Recommended Stack: Next.js + Supabase**

This combination gives you:
- **Next.js**: Server-side rendering for SEO (recipes need to be discoverable)
- **Supabase**: Handles authentication, database, and image storage in one service
- **Vercel**: One-click deployment with automatic image optimization

I found 8 similar recipe/food platforms using this stack on GitHub:
- [Cookpad's recipe platform] migrated from Rails to Next.js for better performance
- [TasteBuddy] uses Supabase for social features and PostgreSQL full-text search
- [RecipeBox] implements image uploads with Supabase Storage

The minimalist alternative would be WordPress with a recipe plugin, which could launch in days rather than weeks. The advanced option would add Algolia for search and Cloudinary for advanced image processing, but that's overkill unless you expect 100k+ recipes.

Implementation would start with:
1. Next.js app with recipe CRUD operations
2. Supabase auth for user accounts  
3. PostgreSQL schema for recipes, ingredients, categories
4. Supabase Storage for photo uploads with automatic resizing
```

## Example 2: Deep Investigation Request

### User Prompt
```bash
/prompt tech-investigator "Explain how Cloudflare Workers handle state since they're stateless at the edge"
```

### Agent Interpretation & Execution
```markdown
INTERPRETING: "how Cloudflare Workers handle state since they're stateless"

DETECTED INTENT: Deep Investigation
- Technology: Cloudflare Workers
- Focus: State management in stateless environment
- Investigating the apparent contradiction

DEEP DIVING INTO CLOUDFLARE WORKERS STATE MANAGEMENT...

You're right that Workers are stateless - each request gets a fresh isolate. Here's how they handle state in practice:

**Discovered State Patterns from Testing:**

1. **Durable Objects** (Stateful actors at the edge)
```javascript
// Tested: Durable Objects maintain state between requests
export class RoomState {
  constructor(state) {
    this.state = state; // Persists between requests!
    this.connections = new Set();
  }
  
  async fetch(request) {
    // This object lives as long as it's needed
    // State survives across multiple requests
  }
}
```
Finding: Single-threaded execution per object, perfect for collaborative features

2. **KV Storage** (Eventually consistent global store)
```javascript
// Tested KV behavior across regions
await env.MY_KV.put('key', 'value', {
  expirationTtl: 60 // seconds
});

// Discovery: ~60 second propagation globally
// Gotcha: Reads might be stale up to 60s in different regions
```

3. **Cache API** (Request-scoped caching)
```javascript
// Workers can cache computed results
const cache = caches.default;
const cached = await cache.match(request);
```
Limit discovered: 50MB per cache entry, 5GB total

**Production Gotchas from GitHub Issues:**

- "Workers KV reads are free but cached for 60s minimum" - Can't invalidate faster
- "Durable Objects are regional" - Cross-region calls add 50-150ms latency  
- "WebSocket state dies after 1 minute idle" - Need heartbeat implementation

**State Decision Tree:**
- Session data → Durable Objects (consistent, fast)
- User preferences → KV Store (eventually consistent OK)
- Computed results → Cache API (request-scoped)
- Real-time collaboration → Durable Objects + WebSockets
- Global configuration → KV Store with local caching
```

## Example 3: Migration Scenario

### User Prompt
```bash
/prompt tech-investigator "Our Django monolith is getting slow, thinking about breaking it into microservices or maybe serverless"
```

### Agent Interpretation & Execution
```markdown
INTERPRETING: "Django monolith is slow, considering microservices or serverless"

DETECTED INTENT: Hybrid Investigation (Migration)
- Current: Django monolith
- Problem: Performance
- Considering: Microservices vs Serverless
- Need: Migration strategy evaluation

ANALYZING MIGRATION OPTIONS...

Before jumping to microservices, I analyzed Django performance bottlenecks from 12 similar migrations on GitHub:

**Common Django Bottlenecks Found:**
- N+1 queries (85% of cases)
- Missing database indexes (70% of cases)  
- Synchronous I/O blocking (60% of cases)
- No caching layer (50% of cases)

**Three Migration Paths Investigated:**

**Option 1: Optimized Django** (2-week effort)
Stay with Django but fix performance:
- Add Redis caching (saw 10x improvement in similar apps)
- Use Django-silk to find slow queries
- Add Celery for async tasks
- Consider Django-channels for WebSockets

GitHub evidence: Spotify still runs Django at massive scale after optimizations

**Option 2: Gradual Extraction** (3-month effort)
Keep Django, extract bottlenecks:
```python
# Pattern from successful migrations
# Step 1: Identify boundary
class OrderService:  # Extract this first
    def process_payment(): # Heaviest operation
    
# Step 2: Deploy as separate service
# FastAPI microservice just for payments
# Django continues handling everything else
```

Success story: Udemy extracted video processing, kept Django for main app

**Option 3: Serverless Functions** (6-month rewrite)
Extract heavy operations to Lambda/Vercel:
- Payment processing → Stripe + Lambda
- Image processing → Cloudflare Workers
- Keep Django for admin and complex business logic

Warning from migrations: "Serverless debugging is painful" appears in 80% of postmortems

**My Recommendation:**
Start with Option 1 (optimize Django). You'll learn what's actually slow. Then selectively apply Option 2 for true bottlenecks. Option 3 only makes sense if you're hitting scale limits Django can't handle (>10k requests/second).

Most "slow Django" apps just need indexes and caching, not microservices.
```

## Example 4: Comparison Request

### User Prompt
```bash
/prompt tech-investigator "Compare Prisma vs Drizzle vs TypeORM for a TypeScript backend"
```

### Agent Interpretation & Execution
```markdown
INTERPRETING: "Compare Prisma vs Drizzle vs TypeORM"

DETECTED INTENT: Stack Selection (Comparison Focus)
- Specific technologies to compare
- Context: TypeScript backend
- Need: ORM comparison

COMPARING ORM OPTIONS FOR TYPESCRIPT...

I tested all three ORMs with the same schema and operations. Here's what I discovered:

**Prisma** (42k GitHub stars, 2.5k production repos analyzed)
- Best DX: Schema-first with auto-generated types
- Migrations: Excellent, automatic from schema
- Performance: Added 15-20ms overhead per query in testing
- Gotcha: Generates 500KB+ client bundle

Real usage: Vercel Commerce, Cal.com, Keystone
Migration pattern: Teams moving FROM TypeORM TO Prisma for better DX

**Drizzle** (15k stars, 400 production repos)
- Philosophy: "If you know SQL, you know Drizzle"  
- Performance: 3-5ms overhead (closest to raw SQL)
- Types: Inferred from schema definition
- Gotcha: Manual migrations, less ecosystem

Real usage: Create-t3-app, smaller startups
Migration pattern: Teams moving FROM Prisma TO Drizzle for performance

**TypeORM** (32k stars, 5k production repos)
- Most mature: 7+ years, battle-tested
- Features: Most complete (subscribers, listeners, etc.)
- Performance: 10-15ms overhead
- Gotcha: Decorators require specific TypeScript config

Real usage: NestJS apps, enterprise
Migration pattern: Stable, not much movement in or out

**Decision Framework:**
- Choose Prisma if: You want best DX and don't mind the bundle size
- Choose Drizzle if: Performance matters and you know SQL well
- Choose TypeORM if: You need advanced features or use NestJS

The trend on GitHub: New projects choose Prisma, performance-critical projects migrate to Drizzle, enterprises stay on TypeORM.
```

## Example 5: Vague Request Requiring Clarification

### User Prompt
```bash
/prompt tech-investigator "What's the best way to build an app?"
```

### Agent Interpretation & Execution
```markdown
INTERPRETING: "best way to build an app"

DETECTED INTENT: Unclear - Need Context
- No specific requirements provided
- "App" could mean mobile, web, desktop
- No scale or constraints mentioned

ADAPTIVE RESPONSE WITH GUIDED EXPLORATION...

I need more context to give you useful technology recommendations. Let me help you clarify by investigating the most common app categories:

**If you're building a mobile app:**
- React Native dominates cross-platform (Facebook, Discord, Pinterest use it)
- Flutter is growing fast (Google Pay, BMW, Alibaba adopted it)
- Native (Swift/Kotlin) only if you need platform-specific features

**If you're building a web application:**
- Next.js + Vercel is the current "default" (used by Netflix, TikTok, Hulu)
- SvelteKit for smaller, faster apps (The New York Times uses it)
- Rails or Django still excellent for CRUD-heavy apps

**If you're building a desktop app:**
- Electron (VS Code, Discord, Slack) - web tech in desktop wrapper
- Tauri (faster, smaller) - Rust-based alternative gaining traction

To give you specific recommendations, could you tell me:
1. What will your app do? (e-commerce, social, productivity, etc.)
2. Who are your users? (consumers, businesses, internal)
3. What's your team's experience? (languages you know)
4. Any constraints? (budget, timeline, scaling needs)

Based on GitHub trends, 70% of new apps in 2024 are using:
- Frontend: React or Next.js
- Backend: Node.js or Python
- Database: PostgreSQL
- Deployment: Vercel or AWS

But the "best" way depends entirely on your specific needs.
```

## Key Patterns in Natural Language Interpretation

The agent responds to natural language by:

1. **Detecting intent from language patterns** rather than parsing flags
2. **Inferring missing context** from what type of thing is being built
3. **Adjusting depth** based on urgency words ("quickly" vs "deep dive")
4. **Providing evidence** regardless of how casual the request
5. **Anticipating follow-ups** based on the type of request

The natural language approach makes the agent feel more like a conversation with a senior architect rather than a command-line tool.