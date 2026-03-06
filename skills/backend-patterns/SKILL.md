---
name: backend-patterns
description: Backend architecture patterns, API design, database optimization, and server-side best practices for Node.js, Express, and Next.js API routes.
---

# Backend Development Patterns

Backend architecture patterns and best practices for scalable server-side applications.

## API Design Patterns

### RESTful API Structure

```typescript
// Resource-based URLs
GET    /api/items                 # List resources
GET    /api/items/:id             # Get single resource
POST   /api/items                 # Create resource
PUT    /api/items/:id             # Replace resource
PATCH  /api/items/:id             # Update resource
DELETE /api/items/:id             # Delete resource

// Query parameters for filtering, sorting, pagination
GET /api/items?status=active&sort=volume&limit=20&offset=0
```

### Repository Pattern

```typescript
// Abstract data access logic
interface ItemRepository {
  findAll(filters?: ItemFilters): Promise<Item[]>
  findById(id: string): Promise<Item | null>
  create(data: CreateItemDto): Promise<Item>
  update(id: string, data: UpdateItemDto): Promise<Item>
  delete(id: string): Promise<void>
}

class DatabaseItemRepository implements ItemRepository {
  async findAll(filters?: ItemFilters): Promise<Item[]> {
    let query = db.from('items').select('*')

    if (filters?.status) {
      query = query.where('status', filters.status)
    }

    if (filters?.limit) {
      query = query.limit(filters.limit)
    }

    const { data, error } = await query

    if (error) throw new Error(error.message)
    return data
  }

  // Other methods...
}
```

### Service Layer Pattern

```typescript
// Business logic separated from data access
class ItemService {
  constructor(private itemRepo: ItemRepository) {}

  async searchItems(query: string, limit: number = 10): Promise<Item[]> {
    // Business logic
    const embedding = await generateEmbedding(query)
    const results = await this.vectorSearch(embedding, limit)

    // Fetch full data
    const items = await this.itemRepo.findByIds(results.map(r => r.id))

    // Sort by similarity
    return items.sort((a, b) => {
      const scoreA = results.find(r => r.id === a.id)?.score || 0
      const scoreB = results.find(r => r.id === b.id)?.score || 0
      return scoreA - scoreB
    })
  }

  private async vectorSearch(embedding: number[], limit: number) {
    // Vector search implementation
  }
}
```

### Middleware Pattern

```typescript
// Request/response processing pipeline
export function withAuth(handler: NextApiHandler): NextApiHandler {
  return async (req, res) => {
    const token = req.headers.authorization?.replace('Bearer ', '')

    if (!token) {
      return res.status(401).json({ error: 'Unauthorized' })
    }

    try {
      const user = await verifyToken(token)
      req.user = user
      return handler(req, res)
    } catch (error) {
      return res.status(401).json({ error: 'Invalid token' })
    }
  }
}

// Usage
export default withAuth(async (req, res) => {
  // Handler has access to req.user
})
```

## Database Patterns

### Query Optimization

```typescript
// GOOD: Select only needed columns
const { data } = await db
  .from('items')
  .select('id, name, status, volume')
  .where('status', 'active')
  .orderBy('volume', 'desc')
  .limit(10)

// BAD: Select everything
const { data } = await db
  .from('items')
  .select('*')
```

### N+1 Query Prevention

```typescript
// BAD: N+1 query problem
const items = await getItems()
for (const item of items) {
  item.creator = await getUser(item.creator_id)  // N queries
}

// GOOD: Batch fetch
const items = await getItems()
const creatorIds = items.map(m => m.creator_id)
const creators = await getUsers(creatorIds)  // 1 query
const creatorMap = new Map(creators.map(c => [c.id, c]))

items.forEach(item => {
  item.creator = creatorMap.get(item.creator_id)
})
```

### Transaction Pattern

```typescript
async function createItemWithRelation(
  itemData: CreateItemDto,
  relationData: CreateRelationDto
) {
  // Use database transaction
  const { data, error } = await db.rpc('create_item_with_relation', {
    item_data: itemData,
    relation_data: relationData
  })

  if (error) throw new Error('Transaction failed')
  return data
}

// SQL function for transaction
CREATE OR REPLACE FUNCTION create_item_with_relation(
  item_data jsonb,
  relation_data jsonb
)
RETURNS jsonb
LANGUAGE plpgsql
AS $$
BEGIN
  -- Start transaction automatically
  INSERT INTO items VALUES (item_data);
  INSERT INTO relations VALUES (relation_data);
  RETURN jsonb_build_object('success', true);
EXCEPTION
  WHEN OTHERS THEN
    -- Rollback happens automatically
    RETURN jsonb_build_object('success', false, 'error', SQLERRM);
END;
$$;
```

## Caching Strategies

### Redis Caching Layer

```typescript
class CachedItemRepository implements ItemRepository {
  constructor(
    private baseRepo: ItemRepository,
    private redis: RedisClient
  ) {}

  async findById(id: string): Promise<Item | null> {
    // Check cache first
    const cached = await this.redis.get(`item:${id}`)

    if (cached) {
      return JSON.parse(cached)
    }

    // Cache miss - fetch from database
    const item = await this.baseRepo.findById(id)

    if (item) {
      // Cache for 5 minutes
      await this.redis.setex(`item:${id}`, 300, JSON.stringify(item))
    }

    return item
  }

  async invalidateCache(id: string): Promise<void> {
    await this.redis.del(`item:${id}`)
  }
}
```

### Cache-Aside Pattern

```typescript
async function getItemWithCache(id: string): Promise<Item> {
  const cacheKey = `item:${id}`

  // Try cache
  const cached = await redis.get(cacheKey)
  if (cached) return JSON.parse(cached)

  // Cache miss - fetch from DB
  const item = await db.items.findUnique({ where: { id } })

  if (!item) throw new Error('Item not found')

  // Update cache
  await redis.setex(cacheKey, 300, JSON.stringify(item))

  return item
}
```

## Error Handling Patterns

### Centralized Error Handler

```typescript
class ApiError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public isOperational = true
  ) {
    super(message)
    Object.setPrototypeOf(this, ApiError.prototype)
  }
}

export function errorHandler(error: unknown, req: Request): Response {
  if (error instanceof ApiError) {
    return NextResponse.json({
      success: false,
      error: error.message
    }, { status: error.statusCode })
  }

  if (error instanceof z.ZodError) {
    return NextResponse.json({
      success: false,
      error: 'Validation failed',
      details: error.errors
    }, { status: 400 })
  }

  // Log unexpected errors
  console.error('Unexpected error:', error)

  return NextResponse.json({
    success: false,
    error: 'Internal server error'
  }, { status: 500 })
}

// Usage
export async function GET(request: Request) {
  try {
    const data = await fetchData()
    return NextResponse.json({ success: true, data })
  } catch (error) {
    return errorHandler(error, request)
  }
}
```

### Retry with Exponential Backoff

```typescript
async function fetchWithRetry<T>(
  fn: () => Promise<T>,
  maxRetries = 3
): Promise<T> {
  let lastError: Error

  for (let i = 0; i < maxRetries; i++) {
    try {
      return await fn()
    } catch (error) {
      lastError = error as Error

      if (i < maxRetries - 1) {
        // Exponential backoff: 1s, 2s, 4s
        const delay = Math.pow(2, i) * 1000
        await new Promise(resolve => setTimeout(resolve, delay))
      }
    }
  }

  throw lastError!
}

// Usage
const data = await fetchWithRetry(() => fetchFromAPI())
```

## Authentication & Authorization

### JWT Token Validation

```typescript
import jwt from 'jsonwebtoken'

interface JWTPayload {
  userId: string
  email: string
  role: 'admin' | 'user'
}

export function verifyToken(token: string): JWTPayload {
  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET!) as JWTPayload
    return payload
  } catch (error) {
    throw new ApiError(401, 'Invalid token')
  }
}

export async function requireAuth(request: Request) {
  const token = request.headers.get('authorization')?.replace('Bearer ', '')

  if (!token) {
    throw new ApiError(401, 'Missing authorization token')
  }

  return verifyToken(token)
}

// Usage in API route
export async function GET(request: Request) {
  const user = await requireAuth(request)

  const data = await getDataForUser(user.userId)

  return NextResponse.json({ success: true, data })
}
```

### Role-Based Access Control

```typescript
type Permission = 'read' | 'write' | 'delete' | 'admin'

interface User {
  id: string
  role: 'admin' | 'moderator' | 'user'
}

const rolePermissions: Record<User['role'], Permission[]> = {
  admin: ['read', 'write', 'delete', 'admin'],
  moderator: ['read', 'write', 'delete'],
  user: ['read', 'write']
}

export function hasPermission(user: User, permission: Permission): boolean {
  return rolePermissions[user.role].includes(permission)
}

export function requirePermission(permission: Permission) {
  return (handler: (request: Request, user: User) => Promise<Response>) => {
    return async (request: Request) => {
      const user = await requireAuth(request)

      if (!hasPermission(user, permission)) {
        throw new ApiError(403, 'Insufficient permissions')
      }

      return handler(request, user)
    }
  }
}

// Usage - HOF wraps the handler
export const DELETE = requirePermission('delete')(
  async (request: Request, user: User) => {
    // Handler receives authenticated user with verified permission
    return new Response('Deleted', { status: 200 })
  }
)
```

## Rate Limiting

### Simple In-Memory Rate Limiter

```typescript
class RateLimiter {
  private requests = new Map<string, number[]>()

  async checkLimit(
    identifier: string,
    maxRequests: number,
    windowMs: number
  ): Promise<boolean> {
    const now = Date.now()
    const requests = this.requests.get(identifier) || []

    // Remove old requests outside window
    const recentRequests = requests.filter(time => now - time < windowMs)

    if (recentRequests.length >= maxRequests) {
      return false  // Rate limit exceeded
    }

    // Add current request
    recentRequests.push(now)
    this.requests.set(identifier, recentRequests)

    return true
  }
}

const limiter = new RateLimiter()

export async function GET(request: Request) {
  const ip = request.headers.get('x-forwarded-for') || 'unknown'

  const allowed = await limiter.checkLimit(ip, 100, 60000)  // 100 req/min

  if (!allowed) {
    return NextResponse.json({
      error: 'Rate limit exceeded'
    }, { status: 429 })
  }

  // Continue with request
}
```

## Background Jobs & Queues

### Simple Queue Pattern

```typescript
class JobQueue<T> {
  private queue: T[] = []
  private processing = false

  async add(job: T): Promise<void> {
    this.queue.push(job)

    if (!this.processing) {
      this.process()
    }
  }

  private async process(): Promise<void> {
    this.processing = true

    while (this.queue.length > 0) {
      const job = this.queue.shift()!

      try {
        await this.execute(job)
      } catch (error) {
        console.error('Job failed:', error)
      }
    }

    this.processing = false
  }

  private async execute(job: T): Promise<void> {
    // Job execution logic
  }
}

// Usage for indexing items
interface IndexJob {
  itemId: string
}

const indexQueue = new JobQueue<IndexJob>()

export async function POST(request: Request) {
  const { itemId } = await request.json()

  // Add to queue instead of blocking
  await indexQueue.add({ itemId })

  return NextResponse.json({ success: true, message: 'Job queued' })
}
```

## Logging & Monitoring

### Structured Logging

```typescript
interface LogContext {
  userId?: string
  requestId?: string
  method?: string
  path?: string
  [key: string]: unknown
}

class Logger {
  log(level: 'info' | 'warn' | 'error', message: string, context?: LogContext) {
    const entry = {
      timestamp: new Date().toISOString(),
      level,
      message,
      ...context
    }

    console.log(JSON.stringify(entry))
  }

  info(message: string, context?: LogContext) {
    this.log('info', message, context)
  }

  warn(message: string, context?: LogContext) {
    this.log('warn', message, context)
  }

  error(message: string, error: Error, context?: LogContext) {
    this.log('error', message, {
      ...context,
      error: error.message,
      stack: error.stack
    })
  }
}

const logger = new Logger()

// Usage
export async function GET(request: Request) {
  const requestId = crypto.randomUUID()

  logger.info('Fetching items', {
    requestId,
    method: 'GET',
    path: '/api/items'
  })

  try {
    const items = await fetchItems()
    return NextResponse.json({ success: true, data: items })
  } catch (error) {
    logger.error('Failed to fetch items', error as Error, { requestId })
    return NextResponse.json({ error: 'Internal error' }, { status: 500 })
  }
}
```

**Remember**: Backend patterns enable scalable, maintainable server-side applications. Choose patterns that fit your complexity level.
