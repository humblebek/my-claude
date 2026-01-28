# Lint Command

Analyze code for style issues, potential bugs, and violations of Laravel best practices.

## Purpose
Ensure code quality, consistency, and adherence to PHP and Laravel standards.

## What This Command Does

1. **Style Checking**
   - PSR-12 compliance
   - Laravel coding standards
   - Naming conventions
   - Indentation and formatting

2. **Static Analysis**
   - Type errors
   - Unused variables
   - Dead code
   - Potential bugs
   - Code complexity

3. **Laravel-Specific Checks**
   - Proper use of Eloquent
   - Route naming consistency
   - Middleware usage
   - Config value usage
   - Translation key usage

4. **Security Checks**
   - SQL injection risks
   - XSS vulnerabilities
   - Mass assignment risks
   - Unvalidated input
   - Exposed secrets

## Usage

```
/lint
```

Then specify what to check:
- "Lint the entire project"
- "Check app/Http/Controllers"
- "Lint UserController.php"
- "Review this code: [paste code]"

## Linting Tools Reference

The command considers outputs from:
- **PHP_CodeSniffer**: PSR-12 compliance
- **PHPStan/Larastan**: Static analysis for Laravel
- **PHP-CS-Fixer**: Auto-fixable style issues
- **Laravel Pint**: Opinionated PHP code style fixer

## Common Issues Detected

### Code Style
```php
// ❌ Wrong
class userController {
    function getUserData($id) {
        return DB::table('users')->where('id',$id)->first();
    }
}

// ✅ Correct
class UserController extends Controller
{
    public function show(User $user): JsonResponse
    {
        return response()->json(new UserResource($user));
    }
}
```

### Type Safety
```php
// ❌ Missing types
function calculate($amount, $tax) {
    return $amount * $tax;
}

// ✅ Proper types
function calculate(float $amount, float $tax): float {
    return $amount * $tax;
}
```

### Laravel Best Practices
```php
// ❌ Hardcoded strings
if ($user->role == 'admin') { }

// ✅ Use constants or enums
if ($user->role === UserRole::ADMIN) { }

// ❌ Not using Form Request
public function store(Request $request) {
    $request->validate([...]);
}

// ✅ Use Form Request
public function store(StoreUserRequest $request) {
    // Validation already done
}
```

### Security Issues
```php
// ❌ SQL Injection risk
DB::select("SELECT * FROM users WHERE email = '".$email."'");

// ✅ Parameter binding
DB::select("SELECT * FROM users WHERE email = ?", [$email]);

// ❌ Mass assignment vulnerability
User::create($request->all());

// ✅ Protected fillable
User::create($request->only(['name', 'email']));
```

### Performance Issues
```php
// ❌ N+1 problem
$users = User::all();
foreach ($users as $user) {
    echo $user->posts->count();
}

// ✅ Eager loading
$users = User::withCount('posts')->get();
```

## Output Format

### Issue Report
```
File: app/Http/Controllers/UserController.php

Line 15: [ERROR] Missing return type declaration
  public function index()
  
  Fix: public function index(): JsonResponse

Line 23: [WARNING] N+1 query detected
  User::all()->each(fn($u) => $u->posts)
  
  Fix: User::with('posts')->get()

Line 45: [SECURITY] Potential SQL injection
  DB::raw("WHERE id = {$id}")
  
  Fix: Use parameter binding
```

### Auto-Fix Suggestions
- Which issues can be auto-fixed
- Commands to run fixes
- Manual fixes needed

### Statistics
- Total issues found
- By severity (Error, Warning, Info)
- By category (Style, Security, Performance)
- Files checked

## Lint Configuration

Recommended `.php-cs-fixer.php`:
```php
<?php

return (new PhpCsFixer\Config())
    ->setRules([
        '@PSR12' => true,
        'array_syntax' => ['syntax' => 'short'],
        'ordered_imports' => ['sort_algorithm' => 'alpha'],
        'no_unused_imports' => true,
    ]);
```

## Fix Command Examples

```bash
# Auto-fix style issues
./vendor/bin/pint

# Run static analysis
./vendor/bin/phpstan analyse

# Check specific file
./vendor/bin/pint app/Http/Controllers/UserController.php
```

## Integration Tips

1. Add to git pre-commit hook
2. Run in CI/CD pipeline
3. Configure IDE to show inline
4. Set up automatic fixes on save
