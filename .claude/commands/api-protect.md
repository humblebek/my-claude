# API Protect Command

Add comprehensive security, validation, and protection layers to API endpoints.

## Purpose
Secure API endpoints with authentication, authorization, rate limiting, input validation, and security best practices.

## What This Command Does

1. **Authentication**
   - Laravel Sanctum token-based auth
   - API key validation
   - JWT integration (if needed)
   - Multi-factor authentication

2. **Authorization**
   - Policy-based access control
   - Role and permission checks
   - Resource ownership validation
   - Attribute-based access control

3. **Input Protection**
   - Request validation
   - Input sanitization
   - SQL injection prevention
   - XSS protection
   - CSRF protection

4. **Rate Limiting**
   - Per-user limits
   - IP-based throttling
   - Custom rate limit rules
   - Distributed rate limiting

5. **Security Headers**
   - CORS configuration
   - Content Security Policy
   - X-Frame-Options
   - X-Content-Type-Options

## Usage

```
/api-protect
```

Then specify what to protect:
- "Add authentication to all API routes"
- "Protect ProductController with policies"
- "Add rate limiting to public API"
- "Secure file upload endpoint"
- "Add validation and sanitization to user input"

## Protection Layers

### 1. Authentication Layer

```php
// routes/api.php
use Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful;

Route::middleware(['auth:sanctum'])->group(function () {
    Route::apiResource('products', ProductController::class);
});

// Alternative: Custom API key authentication
Route::middleware(['auth.apikey'])->group(function () {
    Route::get('/external-data', [ExternalController::class, 'index']);
});
```

```php
// app/Http/Middleware/ApiKeyAuth.php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class ApiKeyAuth
{
    public function handle(Request $request, Closure $next)
    {
        $apiKey = $request->header('X-API-Key');
        
        if (!$apiKey || !$this->isValidApiKey($apiKey)) {
            return response()->json([
                'message' => 'Unauthorized. Invalid API key.'
            ], 401);
        }
        
        return $next($request);
    }
    
    private function isValidApiKey(string $key): bool
    {
        return ApiKey::where('key', $key)
            ->where('is_active', true)
            ->where('expires_at', '>', now())
            ->exists();
    }
}
```

### 2. Authorization (Policies)

```php
// app/Policies/ProductPolicy.php
namespace App\Policies;

use App\Models\Product;
use App\Models\User;

class ProductPolicy
{
    public function viewAny(User $user): bool
    {
        return $user->hasPermissionTo('products.view');
    }

    public function view(User $user, Product $product): bool
    {
        return $user->hasPermissionTo('products.view') ||
               $product->user_id === $user->id;
    }

    public function create(User $user): bool
    {
        return $user->hasPermissionTo('products.create');
    }

    public function update(User $user, Product $product): bool
    {
        return $user->hasPermissionTo('products.update') ||
               ($product->user_id === $user->id && $user->hasPermissionTo('products.update.own'));
    }

    public function delete(User $user, Product $product): bool
    {
        return $user->hasPermissionTo('products.delete') &&
               $product->user_id === $user->id;
    }
}

// Register in AuthServiceProvider
use App\Models\Product;
use App\Policies\ProductPolicy;

protected $policies = [
    Product::class => ProductPolicy::class,
];
```

```php
// Usage in Controller
public function update(UpdateProductRequest $request, Product $product)
{
    $this->authorize('update', $product);
    
    $product->update($request->validated());
    
    return new ProductResource($product);
}
```

### 3. Request Validation & Sanitization

```php
// app/Http/Requests/StoreProductRequest.php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use Illuminate\Validation\Rule;

class StoreProductRequest extends FormRequest
{
    public function authorize(): bool
    {
        return $this->user()->can('create', Product::class);
    }

    public function rules(): array
    {
        return [
            'name' => [
                'required',
                'string',
                'max:255',
                'regex:/^[a-zA-Z0-9\s\-]+$/', // Only alphanumeric and spaces
            ],
            'description' => [
                'nullable',
                'string',
                'max:5000',
            ],
            'price' => [
                'required',
                'numeric',
                'min:0',
                'max:999999.99',
            ],
            'stock' => [
                'required',
                'integer',
                'min:0',
                'max:1000000',
            ],
            'category_id' => [
                'required',
                'exists:categories,id',
            ],
            'tags' => [
                'nullable',
                'array',
                'max:10',
            ],
            'tags.*' => [
                'string',
                'max:50',
            ],
            'image' => [
                'nullable',
                'image',
                'mimes:jpeg,png,jpg,webp',
                'max:5120', // 5MB
            ],
        ];
    }

    protected function prepareForValidation(): void
    {
        // Sanitize inputs
        $this->merge([
            'name' => strip_tags($this->name),
            'description' => strip_tags($this->description),
            'price' => $this->price ? (float) $this->price : null,
        ]);
    }

    public function messages(): array
    {
        return [
            'name.regex' => 'Product name can only contain letters, numbers, spaces, and hyphens.',
            'price.max' => 'Price cannot exceed 999,999.99',
            'image.max' => 'Image size cannot exceed 5MB',
        ];
    }
}
```

### 4. Rate Limiting

```php
// app/Http/Kernel.php
protected $middlewareGroups = [
    'api' => [
        \Illuminate\Routing\Middleware\ThrottleRequests::class.':api',
        // ...
    ],
];
```

```php
// routes/api.php
Route::middleware(['throttle:60,1'])->group(function () {
    Route::get('/public-data', [PublicController::class, 'index']);
});

// Custom rate limiter in RouteServiceProvider
use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

public function boot(): void
{
    RateLimiter::for('api', function (Request $request) {
        return Limit::perMinute(60)->by($request->user()?->id ?: $request->ip());
    });
    
    // Stricter limit for write operations
    RateLimiter::for('api-write', function (Request $request) {
        return Limit::perMinute(10)->by($request->user()?->id);
    });
    
    // Public endpoints with lower limit
    RateLimiter::for('api-public', function (Request $request) {
        return Limit::perMinute(30)->by($request->ip());
    });
}
```

### 5. CORS Configuration

```php
// config/cors.php
return [
    'paths' => ['api/*'],
    'allowed_methods' => ['GET', 'POST', 'PUT', 'DELETE'],
    'allowed_origins' => [
        env('FRONTEND_URL', 'http://localhost:3000'),
    ],
    'allowed_origins_patterns' => [],
    'allowed_headers' => ['Content-Type', 'Authorization', 'X-Requested-With'],
    'exposed_headers' => [],
    'max_age' => 86400,
    'supports_credentials' => true,
];
```

### 6. SQL Injection Prevention

```php
// ❌ VULNERABLE
$email = $request->email;
$user = DB::select("SELECT * FROM users WHERE email = '$email'");

// ✅ SAFE - Parameter binding
$user = DB::select("SELECT * FROM users WHERE email = ?", [$request->email]);

// ✅ SAFE - Query Builder
$user = User::where('email', $request->email)->first();

// ✅ SAFE - Eloquent
$user = User::firstWhere('email', $request->email);
```

### 7. Mass Assignment Protection

```php
// app/Models/Product.php
class Product extends Model
{
    // Whitelist approach (recommended)
    protected $fillable = [
        'name',
        'description',
        'price',
        'stock',
        'category_id',
    ];
    
    // OR Blacklist approach
    protected $guarded = [
        'id',
        'created_at',
        'updated_at',
    ];
}

// Controller
public function store(StoreProductRequest $request)
{
    // ❌ DANGEROUS
    Product::create($request->all());
    
    // ✅ SAFE
    Product::create($request->validated());
    
    // ✅ ALSO SAFE
    Product::create($request->only(['name', 'price', 'stock', 'category_id']));
}
```

### 8. XSS Protection

```php
// Automatic escaping in Blade
{{ $product->name }} // Escaped

// Manual sanitization
use Illuminate\Support\Str;

$clean = Str::of($request->input('text'))
    ->stripTags()
    ->trim()
    ->limit(500);

// In Form Request
protected function prepareForValidation(): void
{
    $this->merge([
        'description' => strip_tags($this->description, '<p><br><b><i><strong><em>'),
    ]);
}
```

### 9. File Upload Security

```php
// app/Http/Requests/UploadImageRequest.php
public function rules(): array
{
    return [
        'image' => [
            'required',
            'file',
            'image',
            'mimes:jpeg,png,jpg,webp',
            'max:5120', // 5MB
            'dimensions:min_width=100,min_height=100,max_width=4000,max_height=4000',
        ],
    ];
}

// Controller
public function upload(UploadImageRequest $request)
{
    $file = $request->file('image');
    
    // Generate safe filename
    $filename = Str::uuid() . '.' . $file->extension();
    
    // Store with proper permissions
    $path = $file->storeAs('products', $filename, 'public');
    
    return response()->json(['path' => $path]);
}
```

### 10. Security Headers Middleware

```php
// app/Http/Middleware/SecurityHeaders.php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;

class SecurityHeaders
{
    public function handle(Request $request, Closure $next)
    {
        $response = $next($request);
        
        $response->headers->set('X-Content-Type-Options', 'nosniff');
        $response->headers->set('X-Frame-Options', 'DENY');
        $response->headers->set('X-XSS-Protection', '1; mode=block');
        $response->headers->set('Referrer-Policy', 'strict-origin-when-cross-origin');
        $response->headers->set('Permissions-Policy', 'geolocation=(), microphone=(), camera=()');
        
        return $response;
    }
}
```

### 11. Audit Logging

```php
// app/Models/AuditLog.php
class AuditLog extends Model
{
    protected $fillable = [
        'user_id',
        'action',
        'model_type',
        'model_id',
        'ip_address',
        'user_agent',
        'changes',
    ];

    protected $casts = [
        'changes' => 'array',
    ];
}

// app/Traits/Auditable.php
trait Auditable
{
    protected static function bootAuditable()
    {
        static::created(function ($model) {
            AuditLog::create([
                'user_id' => auth()->id(),
                'action' => 'created',
                'model_type' => get_class($model),
                'model_id' => $model->id,
                'ip_address' => request()->ip(),
                'user_agent' => request()->userAgent(),
                'changes' => $model->getAttributes(),
            ]);
        });
        
        static::updated(function ($model) {
            AuditLog::create([
                'user_id' => auth()->id(),
                'action' => 'updated',
                'model_type' => get_class($model),
                'model_id' => $model->id,
                'ip_address' => request()->ip(),
                'user_agent' => request()->userAgent(),
                'changes' => $model->getChanges(),
            ]);
        });
    }
}
```

## Security Checklist

✅ Authentication required
✅ Authorization policies implemented
✅ Input validation on all endpoints
✅ SQL injection prevention (parameter binding)
✅ XSS protection (output escaping)
✅ CSRF tokens for state-changing operations
✅ Mass assignment protection
✅ Rate limiting configured
✅ CORS properly configured
✅ Security headers set
✅ File upload validation
✅ Audit logging for sensitive operations
✅ HTTPS enforced in production
✅ Secrets in environment variables
✅ Database encryption for sensitive data
✅ API versioning for backward compatibility

## Environment Variables

```env
# .env
APP_ENV=production
APP_DEBUG=false
APP_URL=https://yourdomain.com

SESSION_SECURE_COOKIE=true
SANCTUM_STATEFUL_DOMAINS=yourdomain.com

DB_CONNECTION=pgsql
DB_SSLMODE=require

RATE_LIMIT_PER_MINUTE=60
RATE_LIMIT_WRITE_PER_MINUTE=10
```

## Testing Security

```php
/** @test */
public function prevents_sql_injection(): void
{
    $response = $this->getJson("/api/products?search='; DROP TABLE products;--");
    $response->assertStatus(200); // Should handle safely
    $this->assertDatabaseHas('products', []); // Table still exists
}

/** @test */
public function rate_limiting_works(): void
{
    for ($i = 0; $i < 61; $i++) {
        $response = $this->getJson('/api/products');
    }
    $response->assertStatus(429);
}
```
