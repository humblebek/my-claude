# Model New Command

Generate Eloquent models with relationships, scopes, accessors, and best practices.

## Purpose
Create feature-complete models with proper type hints, relationships, and business logic.

## Usage
```
/model-new
```

Then describe: "User model with posts relationship", "Product with categories and tags", "Order with items"

## Example Output

```php
// app/Models/Product.php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Relations\BelongsTo;
use Illuminate\Database\Eloquent\Relations\BelongsToMany;
use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Builder;

class Product extends Model
{
    use HasFactory, SoftDeletes;

    protected $fillable = [
        'name',
        'slug',
        'description',
        'price',
        'stock',
        'category_id',
        'is_active',
        'meta',
    ];

    protected $casts = [
        'price' => 'decimal:2',
        'stock' => 'integer',
        'is_active' => 'boolean',
        'meta' => 'array', // For JSONB
    ];

    protected $attributes = [
        'is_active' => true,
        'stock' => 0,
    ];

    // Relationships
    public function category(): BelongsTo
    {
        return $this->belongsTo(Category::class);
    }

    public function tags(): BelongsToMany
    {
        return $this->belongsToMany(Tag::class)->withTimestamps();
    }

    // Scopes
    public function scopeActive(Builder $query): void
    {
        $query->where('is_active', true);
    }

    public function scopeInStock(Builder $query): void
    {
        $query->where('stock', '>', 0);
    }

    public function scopeSearch(Builder $query, string $term): void
    {
        $query->whereRaw("to_tsvector('english', name || ' ' || description) @@ plainto_tsquery('english', ?)", [$term]);
    }

    // Accessors & Mutators
    public function getPriceFormattedAttribute(): string
    {
        return '$' . number_format($this->price, 2);
    }

    public function setNameAttribute(string $value): void
    {
        $this->attributes['name'] = ucfirst($value);
        $this->attributes['slug'] = Str::slug($value);
    }

    // Business Logic
    public function isAvailable(): bool
    {
        return $this->is_active && $this->stock > 0;
    }

    public function reduceStock(int $quantity): bool
    {
        if ($this->stock < $quantity) {
            return false;
        }

        $this->decrement('stock', $quantity);
        return true;
    }
}
```

## Features Included

✅ Type hints for all methods
✅ Relationships with proper return types
✅ Query scopes for common filters
✅ Accessors and mutators
✅ Business logic methods
✅ Proper casts for data types
✅ Default attribute values
✅ Mass assignment protection
✅ Soft deletes (when appropriate)

## Relationship Types

### One-to-Many
```php
// User has many Posts
public function posts(): HasMany
{
    return $this->hasMany(Post::class);
}

// Post belongs to User
public function user(): BelongsTo
{
    return $this->belongsTo(User::class);
}
```

### Many-to-Many
```php
public function tags(): BelongsToMany
{
    return $this->belongsToMany(Tag::class)
        ->withTimestamps()
        ->withPivot('order');
}
```

### Polymorphic
```php
public function commentable(): MorphTo
{
    return $this->morphTo();
}

public function comments(): MorphMany
{
    return $this->morphMany(Comment::class, 'commentable');
}
```
