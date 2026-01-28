# Code Cleanup Command

Refactor code for better maintainability, readability, and adherence to Laravel best practices.

## Purpose
Clean up technical debt, improve code structure, and ensure consistent coding standards.

## What This Command Does

1. **Code Refactoring**
   - Extract complex logic into methods
   - Improve naming conventions
   - Reduce code duplication
   - Simplify conditional logic
   - Remove dead code

2. **Laravel Best Practices**
   - Follow PSR-12 coding standards
   - Use Form Requests for validation
   - Implement Repository pattern where needed
   - Use API Resources for responses
   - Apply Service layer for business logic
   - Use Events for decoupled operations

3. **SOLID Principles**
   - Single Responsibility
   - Open/Closed
   - Liskov Substitution
   - Interface Segregation
   - Dependency Inversion

4. **Code Quality**
   - Reduce cyclomatic complexity
   - Improve testability
   - Add type hints
   - Improve error handling
   - Add meaningful comments

## Usage

```
/code-cleanup
```

Then specify what to clean:
- "Cleanup UserController"
- "Refactor authentication logic"
- "Clean up this method: [paste code]"
- "Review and cleanup app/Services/OrderService.php"

## Cleanup Categories

### Structure Improvements
- Extract long methods
- Create dedicated classes for complex logic
- Implement design patterns
- Organize use statements
- Group related functionality

### Naming Improvements
- Use descriptive variable names
- Follow Laravel naming conventions
- Use intention-revealing names
- Avoid abbreviations

### Laravel-Specific Cleanup
- Replace raw queries with Query Builder
- Use Eloquent scopes
- Implement custom validation rules
- Use Route model binding
- Leverage middleware
- Use config values instead of hardcoded strings

### Database Cleanup
- Use migrations properly
- Add foreign key constraints
- Implement soft deletes where appropriate
- Use database transactions
- Add proper indexes

## Example Transformations

### Before Cleanup ❌
```php
public function getUserData($id) {
    $u = DB::table('users')->where('id', $id)->first();
    if($u) {
        $posts = DB::table('posts')->where('user_id', $id)->get();
        $data = [
            'name' => $u->name,
            'email' => $u->email,
            'posts' => $posts
        ];
        return $data;
    }
    return null;
}
```

### After Cleanup ✅
```php
public function show(User $user): UserResource
{
    return new UserResource(
        $user->load('posts')
    );
}
```

## Output Includes

1. **Refactored Code**: Clean, production-ready implementation
2. **Changes Summary**: What was improved and why
3. **Additional Recommendations**: Further improvements
4. **Testing Notes**: How to verify the changes work

## Focus Areas

- Remove code duplication (DRY principle)
- Simplify complex conditionals
- Extract magic numbers to constants
- Use dependency injection
- Improve error messages
- Add type declarations
- Use strict types
- Remove unused imports and variables
- Consistent code formatting
