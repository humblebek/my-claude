# Code Optimize Command

Analyze and optimize code for performance, focusing on Laravel and PostgreSQL best practices.

## Purpose
Identify performance bottlenecks and provide optimized implementations with measurable improvements.

## What This Command Does

1. **Performance Analysis**
   - Database query optimization (N+1, missing indexes)
   - Memory usage patterns
   - Caching opportunities
   - Unnecessary computations
   - Inefficient algorithms

2. **Laravel-Specific Optimization**
   - Eager loading relationships
   - Query scope optimization
   - Cache strategy implementation
   - Queue usage for heavy tasks
   - Chunk processing for large datasets
   - Database connection optimization

3. **PostgreSQL Optimization**
   - Query plan analysis
   - Index suggestions
   - JSONB query optimization
   - Proper JOIN strategies
   - Connection pooling
   - Vacuum and analyze recommendations

4. **Code Improvements**
   - Refactor inefficient loops
   - Reduce database round trips
   - Implement caching layers
   - Optimize file operations
   - Reduce memory footprint

## Usage

```
/code-optimize
```

Then specify what to optimize:
- "Optimize UserController index method"
- "Improve performance of product search"
- "Optimize this query: [paste query]"
- "Review database queries in OrderService"

## Optimization Categories

### Database Optimization
- **Eager Loading**: Prevent N+1 queries
- **Indexing**: Suggest missing indexes
- **Query Simplification**: Reduce complexity
- **Pagination**: Implement cursor pagination for large datasets
- **Caching**: Cache expensive queries

### Code Optimization
- **Algorithm Efficiency**: O(n) to O(log n) improvements
- **Memory Management**: Reduce memory usage
- **Collection Methods**: Use efficient Laravel collection methods
- **Lazy Loading**: Defer expensive operations

### Infrastructure
- **Redis/Memcached**: Caching strategy
- **Queue Workers**: Offload heavy tasks
- **CDN**: Static asset delivery
- **Connection Pooling**: Database connection optimization

## Output Format

### Before/After Comparison
```php
// Before (❌ N+1 queries)
$users = User::all();
foreach ($users as $user) {
    echo $user->posts->count();
}

// After (✅ Single query with eager loading)
$users = User::withCount('posts')->get();
foreach ($users as $user) {
    echo $user->posts_count;
}
```

### Performance Metrics
- Query count reduction
- Response time improvement
- Memory usage reduction
- Database load reduction

### Implementation Steps
1. Specific code changes
2. Migration changes (if needed)
3. Configuration updates
4. Testing approach

## PostgreSQL-Specific Optimizations

- Use EXPLAIN ANALYZE for query plans
- Implement partial indexes
- Use JSONB operators efficiently
- Leverage full-text search
- Implement table partitioning for large tables
- Use materialized views for complex aggregations
