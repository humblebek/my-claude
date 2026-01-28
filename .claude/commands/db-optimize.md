# Database Optimize Command

Analyze and optimize database performance with PostgreSQL-specific optimizations.

## Purpose
Identify slow queries, missing indexes, and performance bottlenecks in PostgreSQL databases.

## Usage
```
/db-optimize
```

Then specify: "Optimize products table queries", "Fix N+1 queries in OrderController", "Analyze slow query"

## Optimization Strategies

### 1. Index Analysis
```sql
-- Find missing indexes
SELECT schemaname, tablename, attname
FROM pg_stats
WHERE schemaname = 'public'
AND n_distinct > 100
AND correlation < 0.5
ORDER BY n_distinct DESC;

-- Index usage stats
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan,
    idx_tup_read,
    idx_tup_fetch
FROM pg_stat_user_indexes
WHERE idx_scan = 0
ORDER BY schemaname, tablename;
```

### 2. Query Performance
```php
// ❌ N+1 Problem
$users = User::all();
foreach ($users as $user) {
    echo $user->posts->count(); // N queries
}

// ✅ Eager Loading
$users = User::withCount('posts')->get(); // 1 query
```

### 3. Add Indexes
```php
// Migration
Schema::table('products', function (Blueprint $table) {
    // Single column index
    $table->index('category_id');
    
    // Composite index
    $table->index(['is_active', 'created_at']);
    
    // Unique index
    $table->unique('slug');
    
    // Partial index (PostgreSQL)
    DB::statement('CREATE INDEX active_products_idx ON products (created_at) WHERE is_active = true');
    
    // GIN index for JSONB
    DB::statement('CREATE INDEX products_meta_idx ON products USING gin(meta)');
});
```

### 4. Query Optimization
```php
// Use select() to limit columns
Product::select('id', 'name', 'price')->get();

// Use chunk() for large datasets
Product::chunk(1000, function ($products) {
    foreach ($products as $product) {
        // Process
    }
});

// Use cursor() for memory efficiency
foreach (Product::cursor() as $product) {
    // Process
}
```

### 5. Caching
```php
// Cache expensive queries
$products = Cache::remember('products.active', 3600, function () {
    return Product::with('category')->active()->get();
});

// Cache count queries
$count = Cache::remember('products.count', 600, function () {
    return Product::count();
});
```

### 6. Database Connection Pooling
```php
// config/database.php
'pgsql' => [
    'driver' => 'pgsql',
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', '5432'),
    'database' => env('DB_DATABASE', 'forge'),
    'username' => env('DB_USERNAME', 'forge'),
    'password' => env('DB_PASSWORD', ''),
    'charset' => 'utf8',
    'prefix' => '',
    'schema' => 'public',
    'sslmode' => 'prefer',
    'pooling' => true, // Enable pooling
],
```

## PostgreSQL-Specific Optimizations

### EXPLAIN ANALYZE
```sql
EXPLAIN ANALYZE
SELECT * FROM products
WHERE category_id = 1
AND is_active = true
ORDER BY created_at DESC
LIMIT 10;
```

### Vacuum and Analyze
```bash
# Regular maintenance
php artisan db:vacuum

# Or manually
psql -c "VACUUM ANALYZE products;"
```

### Materialized Views
```sql
CREATE MATERIALIZED VIEW product_stats AS
SELECT
    category_id,
    COUNT(*) as total_products,
    AVG(price) as avg_price,
    SUM(stock) as total_stock
FROM products
WHERE is_active = true
GROUP BY category_id;

-- Refresh periodically
REFRESH MATERIALIZED VIEW product_stats;
```

## Performance Checklist

✅ Indexes on foreign keys
✅ Indexes on frequently filtered columns
✅ Composite indexes for multi-column queries
✅ Eager loading relationships
✅ Pagination for large result sets
✅ Query result caching
✅ Database connection pooling
✅ Regular VACUUM ANALYZE
✅ Monitor slow query log
✅ Use explain analyze for optimization
