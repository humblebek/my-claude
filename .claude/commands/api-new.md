# API New Command

Create a complete RESTful API endpoint with all necessary components.

## Purpose
Scaffold a production-ready API endpoint following Laravel best practices with proper validation, resources, and error handling.

## What This Command Does

1. **Generate Files**
   - Controller with CRUD methods
   - Form Request for validation
   - API Resource for response formatting
   - Model (if needed)
   - Migration (if needed)
   - Factory for testing
   - Feature tests

2. **Implement Features**
   - RESTful endpoints
   - Input validation
   - Authorization policies
   - Error handling
   - Pagination
   - Filtering and sorting
   - API versioning ready

3. **Security**
   - CSRF protection
   - Rate limiting
   - Input sanitization
   - SQL injection prevention
   - XSS protection

## Usage

```
/api-new
```

Then describe your API:
- "Create CRUD API for Product with category relationship"
- "Build API for blog posts with comments"
- "Generate User management API with roles"
- "Create API for file uploads"

## Generated Structure

For example: `/api-new` with "Product API"

### 1. Migration
```php
// database/migrations/xxxx_create_products_table.php
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->text('description')->nullable();
    $table->decimal('price', 10, 2);
    $table->integer('stock')->default(0);
    $table->foreignId('category_id')->constrained()->cascadeOnDelete();
    $table->boolean('is_active')->default(true);
    $table->timestamps();
    $table->softDeletes();
    
    $table->index(['is_active', 'created_at']);
    $table->index('category_id');
});
```

### 2. Model
```php
// app/Models/Product.php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\SoftDeletes;

class Product extends Model
{
    use HasFactory, SoftDeletes;

    protected $fillable = [
        'name',
        'description',
        'price',
        'stock',
        'category_id',
        'is_active',
    ];

    protected $casts = [
        'price' => 'decimal:2',
        'stock' => 'integer',
        'is_active' => 'boolean',
    ];

    // Relationships
    public function category()
    {
        return $this->belongsTo(Category::class);
    }

    // Scopes
    public function scopeActive($query)
    {
        return $query->where('is_active', true);
    }

    public function scopeInStock($query)
    {
        return $query->where('stock', '>', 0);
    }
}
```

### 3. Form Requests
```php
// app/Http/Requests/StoreProductRequest.php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreProductRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Product::class);
    }

    public function rules(): array
    {
        return [
            'name' => ['required', 'string', 'max:255'],
            'description' => ['nullable', 'string', 'max:5000'],
            'price' => ['required', 'numeric', 'min:0'],
            'stock' => ['required', 'integer', 'min:0'],
            'category_id' => ['required', 'exists:categories,id'],
            'is_active' => ['boolean'],
        ];
    }

    public function messages(): array
    {
        return [
            'name.required' => 'Product name is required',
            'price.min' => 'Price cannot be negative',
            'category_id.exists' => 'Selected category does not exist',
        ];
    }
}

// app/Http/Requests/UpdateProductRequest.php
class UpdateProductRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('update', $this->route('product'));
    }

    public function rules(): array
    {
        return [
            'name' => ['sometimes', 'string', 'max:255'],
            'description' => ['nullable', 'string', 'max:5000'],
            'price' => ['sometimes', 'numeric', 'min:0'],
            'stock' => ['sometimes', 'integer', 'min:0'],
            'category_id' => ['sometimes', 'exists:categories,id'],
            'is_active' => ['boolean'],
        ];
    }
}
```

### 4. API Resource
```php
// app/Http/Resources/ProductResource.php
namespace App\Http\Resources;

use Illuminate\Http\Request;
use Illuminate\Http\Resources\Json\JsonResource;

class ProductResource extends JsonResource
{
    public function toArray(Request $request): array
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'description' => $this->description,
            'price' => $this->price,
            'stock' => $this->stock,
            'is_active' => $this->is_active,
            'category' => new CategoryResource($this->whenLoaded('category')),
            'created_at' => $this->created_at,
            'updated_at' => $this->updated_at,
        ];
    }
}
```

### 5. Controller
```php
// app/Http/Controllers/Api/ProductController.php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Http\Requests\StoreProductRequest;
use App\Http\Requests\UpdateProductRequest;
use App\Http\Resources\ProductResource;
use App\Models\Product;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;

class ProductController extends Controller
{
    /**
     * Display a listing of products.
     */
    public function index(Request $request)
    {
        $products = Product::query()
            ->with('category')
            ->when($request->search, function ($query, $search) {
                $query->where('name', 'ILIKE', "%{$search}%")
                      ->orWhere('description', 'ILIKE', "%{$search}%");
            })
            ->when($request->category_id, function ($query, $categoryId) {
                $query->where('category_id', $categoryId);
            })
            ->when($request->is_active !== null, function ($query) use ($request) {
                $query->where('is_active', $request->boolean('is_active'));
            })
            ->orderBy($request->get('sort_by', 'created_at'), $request->get('sort_order', 'desc'))
            ->paginate($request->get('per_page', 15));

        return ProductResource::collection($products);
    }

    /**
     * Store a newly created product.
     */
    public function store(StoreProductRequest $request): JsonResponse
    {
        $product = Product::create($request->validated());
        
        $product->load('category');

        return (new ProductResource($product))
            ->response()
            ->setStatusCode(201);
    }

    /**
     * Display the specified product.
     */
    public function show(Product $product): ProductResource
    {
        $product->load('category');
        
        return new ProductResource($product);
    }

    /**
     * Update the specified product.
     */
    public function update(UpdateProductRequest $request, Product $product): ProductResource
    {
        $product->update($request->validated());
        
        $product->load('category');

        return new ProductResource($product);
    }

    /**
     * Remove the specified product.
     */
    public function destroy(Product $product): JsonResponse
    {
        $this->authorize('delete', $product);
        
        $product->delete();

        return response()->json(null, 204);
    }
}
```

### 6. Routes
```php
// routes/api.php
use App\Http\Controllers\Api\ProductController;

Route::middleware(['auth:sanctum'])->group(function () {
    Route::apiResource('products', ProductController::class);
});
```

### 7. Policy
```php
// app/Policies/ProductPolicy.php
namespace App\Policies;

use App\Models\Product;
use App\Models\User;

class ProductPolicy
{
    public function viewAny(User $user): bool
    {
        return true;
    }

    public function view(User $user, Product $product): bool
    {
        return true;
    }

    public function create(User $user): bool
    {
        return $user->hasPermission('products.create');
    }

    public function update(User $user, Product $product): bool
    {
        return $user->hasPermission('products.update');
    }

    public function delete(User $user, Product $product): bool
    {
        return $user->hasPermission('products.delete');
    }
}
```

### 8. Factory
```php
// database/factories/ProductFactory.php
namespace Database\Factories;

use App\Models\Category;
use Illuminate\Database\Eloquent\Factories\Factory;

class ProductFactory extends Factory
{
    public function definition(): array
    {
        return [
            'name' => fake()->words(3, true),
            'description' => fake()->paragraph(),
            'price' => fake()->randomFloat(2, 10, 1000),
            'stock' => fake()->numberBetween(0, 100),
            'category_id' => Category::factory(),
            'is_active' => fake()->boolean(80),
        ];
    }
}
```

### 9. Tests
```php
// tests/Feature/ProductApiTest.php
namespace Tests\Feature;

use App\Models\Product;
use App\Models\User;
use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ProductApiTest extends TestCase
{
    use RefreshDatabase;

    public function test_can_list_products(): void
    {
        Product::factory()->count(3)->create();

        $response = $this->actingAs(User::factory()->create())
            ->getJson('/api/products');

        $response->assertStatus(200)
            ->assertJsonStructure([
                'data' => [
                    '*' => ['id', 'name', 'price', 'stock']
                ]
            ]);
    }

    public function test_can_create_product(): void
    {
        $user = User::factory()->create();
        
        $response = $this->actingAs($user)
            ->postJson('/api/products', [
                'name' => 'Test Product',
                'price' => 99.99,
                'stock' => 10,
                'category_id' => Category::factory()->create()->id,
            ]);

        $response->assertStatus(201)
            ->assertJsonFragment(['name' => 'Test Product']);
            
        $this->assertDatabaseHas('products', ['name' => 'Test Product']);
    }
}
```

## Features Included

✅ RESTful endpoints (GET, POST, PUT, DELETE)
✅ Request validation with custom rules
✅ API Resources for response formatting
✅ Query filtering and search
✅ Pagination
✅ Sorting
✅ Eager loading to prevent N+1
✅ Authorization policies
✅ Soft deletes
✅ Factory for testing
✅ Feature tests
✅ PostgreSQL ILIKE for case-insensitive search
✅ Proper HTTP status codes
✅ Error handling

## API Endpoints Generated

```
GET    /api/products              # List with pagination
POST   /api/products              # Create new
GET    /api/products/{id}         # Show single
PUT    /api/products/{id}         # Update
DELETE /api/products/{id}         # Delete (soft)

Query parameters:
- page, per_page (pagination)
- search (full-text)
- category_id (filter)
- is_active (filter)
- sort_by, sort_order (sorting)
```

## Response Examples

### Success Response (200)
```json
{
  "data": [
    {
      "id": 1,
      "name": "Product Name",
      "description": "Product description",
      "price": "99.99",
      "stock": 50,
      "is_active": true,
      "category": {
        "id": 1,
        "name": "Category Name"
      },
      "created_at": "2024-01-15T10:30:00.000000Z",
      "updated_at": "2024-01-15T10:30:00.000000Z"
    }
  ],
  "links": {...},
  "meta": {...}
}
```

### Error Response (422)
```json
{
  "message": "The given data was invalid.",
  "errors": {
    "name": ["Product name is required"],
    "price": ["Price cannot be negative"]
  }
}
```
