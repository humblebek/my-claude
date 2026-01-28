# API Test Command

Generate comprehensive test suites for API endpoints with various scenarios.

## Purpose
Create thorough feature tests covering happy paths, edge cases, validation, authorization, and error scenarios.

## What This Command Does

1. **Test Generation**
   - Feature tests for all CRUD operations
   - Validation tests
   - Authorization tests
   - Edge case scenarios
   - Performance tests

2. **Test Coverage**
   - Success scenarios (2xx responses)
   - Client errors (4xx responses)
   - Server errors (5xx responses)
   - Authentication/Authorization
   - Rate limiting
   - Pagination

3. **Database Management**
   - Factory usage
   - Database transactions
   - Seeding test data
   - Cleanup strategies

## Usage

```
/api-test
```

Then specify what to test:
- "Generate tests for ProductController API"
- "Create test suite for user authentication"
- "Test API endpoint: POST /api/orders"
- "Generate comprehensive tests for existing API"

## Generated Test Structure

### Complete Test Suite Example

```php
// tests/Feature/ProductApiTest.php
namespace Tests\Feature;

use App\Models\Product;
use App\Models\Category;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Laravel\Sanctum\Sanctum;
use Tests\TestCase;

class ProductApiTest extends TestCase
{
    use RefreshDatabase;

    private User $user;
    private User $admin;

    protected function setUp(): void
    {
        parent::setUp();
        
        $this->user = User::factory()->create();
        $this->admin = User::factory()->admin()->create();
    }

    // ==================== INDEX TESTS ====================

    /** @test */
    public function guest_cannot_list_products(): void
    {
        $response = $this->getJson('/api/products');

        $response->assertStatus(401);
    }

    /** @test */
    public function authenticated_user_can_list_products(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->count(5)->create();

        $response = $this->getJson('/api/products');

        $response->assertStatus(200)
            ->assertJsonStructure([
                'data' => [
                    '*' => [
                        'id',
                        'name',
                        'description',
                        'price',
                        'stock',
                        'is_active',
                        'category',
                        'created_at',
                        'updated_at'
                    ]
                ],
                'links',
                'meta'
            ])
            ->assertJsonCount(5, 'data');
    }

    /** @test */
    public function can_filter_products_by_category(): void
    {
        Sanctum::actingAs($this->user);
        
        $category = Category::factory()->create();
        Product::factory()->count(3)->create(['category_id' => $category->id]);
        Product::factory()->count(2)->create(); // Different category

        $response = $this->getJson("/api/products?category_id={$category->id}");

        $response->assertStatus(200)
            ->assertJsonCount(3, 'data');
    }

    /** @test */
    public function can_search_products_by_name(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->create(['name' => 'iPhone 15']);
        Product::factory()->create(['name' => 'Samsung Galaxy']);
        Product::factory()->create(['name' => 'iPad Pro']);

        $response = $this->getJson('/api/products?search=iPhone');

        $response->assertStatus(200)
            ->assertJsonCount(1, 'data')
            ->assertJsonFragment(['name' => 'iPhone 15']);
    }

    /** @test */
    public function can_sort_products(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->create(['price' => 100]);
        Product::factory()->create(['price' => 50]);
        Product::factory()->create(['price' => 200]);

        $response = $this->getJson('/api/products?sort_by=price&sort_order=asc');

        $response->assertStatus(200);
        $prices = collect($response->json('data'))->pluck('price')->toArray();
        $this->assertEquals(['50.00', '100.00', '200.00'], $prices);
    }

    /** @test */
    public function products_are_paginated(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->count(25)->create();

        $response = $this->getJson('/api/products?per_page=10');

        $response->assertStatus(200)
            ->assertJsonCount(10, 'data')
            ->assertJsonPath('meta.total', 25)
            ->assertJsonPath('meta.per_page', 10);
    }

    // ==================== SHOW TESTS ====================

    /** @test */
    public function can_view_single_product(): void
    {
        Sanctum::actingAs($this->user);
        
        $product = Product::factory()->create([
            'name' => 'Test Product',
            'price' => 99.99
        ]);

        $response = $this->getJson("/api/products/{$product->id}");

        $response->assertStatus(200)
            ->assertJsonFragment([
                'id' => $product->id,
                'name' => 'Test Product',
                'price' => '99.99'
            ]);
    }

    /** @test */
    public function returns_404_for_nonexistent_product(): void
    {
        Sanctum::actingAs($this->user);

        $response = $this->getJson('/api/products/99999');

        $response->assertStatus(404);
    }

    // ==================== STORE TESTS ====================

    /** @test */
    public function authorized_user_can_create_product(): void
    {
        Sanctum::actingAs($this->admin);
        
        $category = Category::factory()->create();
        
        $data = [
            'name' => 'New Product',
            'description' => 'Product description',
            'price' => 149.99,
            'stock' => 20,
            'category_id' => $category->id,
            'is_active' => true
        ];

        $response = $this->postJson('/api/products', $data);

        $response->assertStatus(201)
            ->assertJsonFragment(['name' => 'New Product']);
            
        $this->assertDatabaseHas('products', [
            'name' => 'New Product',
            'price' => 149.99
        ]);
    }

    /** @test */
    public function unauthorized_user_cannot_create_product(): void
    {
        Sanctum::actingAs($this->user); // Regular user without permission

        $response = $this->postJson('/api/products', [
            'name' => 'New Product',
            'price' => 100,
            'stock' => 10,
            'category_id' => Category::factory()->create()->id
        ]);

        $response->assertStatus(403);
    }

    /** @test */
    public function product_creation_requires_name(): void
    {
        Sanctum::actingAs($this->admin);

        $response = $this->postJson('/api/products', [
            'price' => 100,
            'stock' => 10,
            'category_id' => Category::factory()->create()->id
        ]);

        $response->assertStatus(422)
            ->assertJsonValidationErrors(['name']);
    }

    /** @test */
    public function product_price_must_be_positive(): void
    {
        Sanctum::actingAs($this->admin);

        $response = $this->postJson('/api/products', [
            'name' => 'Test',
            'price' => -10,
            'stock' => 10,
            'category_id' => Category::factory()->create()->id
        ]);

        $response->assertStatus(422)
            ->assertJsonValidationErrors(['price']);
    }

    /** @test */
    public function product_requires_valid_category(): void
    {
        Sanctum::actingAs($this->admin);

        $response = $this->postJson('/api/products', [
            'name' => 'Test',
            'price' => 100,
            'stock' => 10,
            'category_id' => 99999 // Non-existent
        ]);

        $response->assertStatus(422)
            ->assertJsonValidationErrors(['category_id']);
    }

    // ==================== UPDATE TESTS ====================

    /** @test */
    public function authorized_user_can_update_product(): void
    {
        Sanctum::actingAs($this->admin);
        
        $product = Product::factory()->create(['name' => 'Old Name']);

        $response = $this->putJson("/api/products/{$product->id}", [
            'name' => 'Updated Name',
            'price' => 199.99
        ]);

        $response->assertStatus(200)
            ->assertJsonFragment(['name' => 'Updated Name']);
            
        $this->assertDatabaseHas('products', [
            'id' => $product->id,
            'name' => 'Updated Name'
        ]);
    }

    /** @test */
    public function cannot_update_to_invalid_price(): void
    {
        Sanctum::actingAs($this->admin);
        
        $product = Product::factory()->create();

        $response = $this->putJson("/api/products/{$product->id}", [
            'price' => -50
        ]);

        $response->assertStatus(422)
            ->assertJsonValidationErrors(['price']);
    }

    // ==================== DELETE TESTS ====================

    /** @test */
    public function authorized_user_can_delete_product(): void
    {
        Sanctum::actingAs($this->admin);
        
        $product = Product::factory()->create();

        $response = $this->deleteJson("/api/products/{$product->id}");

        $response->assertStatus(204);
        $this->assertSoftDeleted('products', ['id' => $product->id]);
    }

    /** @test */
    public function unauthorized_user_cannot_delete_product(): void
    {
        Sanctum::actingAs($this->user);
        
        $product = Product::factory()->create();

        $response = $this->deleteJson("/api/products/{$product->id}");

        $response->assertStatus(403);
    }

    // ==================== EDGE CASES ====================

    /** @test */
    public function handles_large_result_sets_efficiently(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->count(1000)->create();

        $startTime = microtime(true);
        $response = $this->getJson('/api/products?per_page=100');
        $endTime = microtime(true);

        $response->assertStatus(200);
        $this->assertLessThan(1, $endTime - $startTime); // Should respond in under 1 second
    }

    /** @test */
    public function prevents_n_plus_one_queries(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->count(10)->create();

        $this->assertQueryCount(2, function () { // 1 for products, 1 for categories
            $this->getJson('/api/products');
        });
    }

    /** @test */
    public function handles_special_characters_in_search(): void
    {
        Sanctum::actingAs($this->user);
        
        Product::factory()->create(['name' => "Product's Name & Special <chars>"]);

        $response = $this->getJson('/api/products?search=' . urlencode("Product's"));

        $response->assertStatus(200)
            ->assertJsonCount(1, 'data');
    }
}
```

## Test Categories

### 1. Authentication Tests
```php
/** @test */
public function guest_cannot_access_api(): void
{
    $response = $this->getJson('/api/products');
    $response->assertStatus(401);
}
```

### 2. Authorization Tests
```php
/** @test */
public function user_without_permission_cannot_create(): void
{
    Sanctum::actingAs($this->user);
    $response = $this->postJson('/api/products', $data);
    $response->assertStatus(403);
}
```

### 3. Validation Tests
```php
/** @test */
public function validates_required_fields(): void
{
    $response = $this->postJson('/api/products', []);
    $response->assertStatus(422)
        ->assertJsonValidationErrors(['name', 'price', 'category_id']);
}
```

### 4. Performance Tests
```php
/** @test */
public function response_time_is_acceptable(): void
{
    $start = microtime(true);
    $this->getJson('/api/products');
    $duration = microtime(true) - $start;
    
    $this->assertLessThan(0.5, $duration);
}
```

### 5. Rate Limiting Tests
```php
/** @test */
public function api_is_rate_limited(): void
{
    Sanctum::actingAs($this->user);
    
    for ($i = 0; $i < 60; $i++) {
        $this->getJson('/api/products');
    }
    
    $response = $this->getJson('/api/products');
    $response->assertStatus(429); // Too Many Requests
}
```

## Running Tests

```bash
# Run all tests
php artisan test

# Run specific test file
php artisan test tests/Feature/ProductApiTest.php

# Run specific test method
php artisan test --filter=can_create_product

# Run with coverage
php artisan test --coverage

# Run with parallel execution
php artisan test --parallel
```

## Test Helpers

```php
// Custom assertions
protected function assertQueryCount(int $expected, callable $callback): void
{
    $queries = 0;
    DB::listen(function () use (&$queries) {
        $queries++;
    });
    
    $callback();
    
    $this->assertEquals($expected, $queries);
}

// Custom factory states
$user = User::factory()->admin()->create();
$product = Product::factory()->inactive()->create();
```

## Coverage Goals

- ✅ 100% endpoint coverage
- ✅ All validation rules tested
- ✅ All authorization policies tested
- ✅ Happy path scenarios
- ✅ Error scenarios
- ✅ Edge cases
- ✅ Performance checks
