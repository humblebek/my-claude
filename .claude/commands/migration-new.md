# Migration New Command

Create comprehensive database migrations with proper schema design.

## Purpose
Generate well-structured migrations with indexes, foreign keys, and PostgreSQL-specific features.

## Usage
```
/migration-new
```

Then describe: "Create users table with roles", "Add soft deletes to products", "Create pivot table for many-to-many"

## Example Output

```php
// database/migrations/xxxx_create_products_table.php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('products', function (Blueprint $table) {
            $table->id();
            $table->string('name')->index();
            $table->string('slug')->unique();
            $table->text('description')->nullable();
            $table->decimal('price', 10, 2);
            $table->integer('stock')->default(0);
            $table->foreignId('category_id')
                  ->constrained()
                  ->cascadeOnDelete();
            $table->boolean('is_active')->default(true);
            $table->jsonb('meta')->nullable(); // PostgreSQL JSONB
            $table->timestamps();
            $table->softDeletes();
            
            // Indexes
            $table->index(['is_active', 'created_at']);
            $table->index('category_id');
            
            // Full-text search (PostgreSQL)
            $table->rawIndex('to_tsvector(\'english\', name || \' \' || description)', 'products_search_idx', 'gin');
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('products');
    }
};
```

## PostgreSQL Features

### JSONB Columns
```php
$table->jsonb('settings')->nullable();
$table->jsonb('metadata')->default('{}');
```

### Full-Text Search
```php
DB::statement('CREATE INDEX products_search_idx ON products USING gin(to_tsvector(\'english\', name || \' \' || description))');
```

### Array Columns
```php
$table->json('tags')->nullable(); // Use as array
```

### Enums
```php
$table->enum('status', ['draft', 'published', 'archived'])->default('draft');
```

## Best Practices

✅ Always add indexes for foreign keys
✅ Use appropriate column types
✅ Add default values where sensible
✅ Include timestamps
✅ Use soft deletes for important data
✅ Add unique constraints
✅ Document complex schemas
✅ Consider partitioning for large tables

## Common Patterns

### Pivot Table
```php
Schema::create('product_tag', function (Blueprint $table) {
    $table->foreignId('product_id')->constrained()->cascadeOnDelete();
    $table->foreignId('tag_id')->constrained()->cascadeOnDelete();
    $table->timestamps();
    
    $table->primary(['product_id', 'tag_id']);
});
```

### Polymorphic
```php
$table->morphs('commentable'); // Creates commentable_type and commentable_id
```
