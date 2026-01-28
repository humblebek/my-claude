# Documentation Generate Command

Automatically generate comprehensive documentation for Laravel projects.

## Purpose
Create and maintain high-quality documentation for APIs, database schemas, and code architecture.

## What This Command Does

1. **API Documentation**
   - Endpoint descriptions
   - Request/Response examples
   - Authentication requirements
   - Error responses
   - Rate limiting info

2. **Database Documentation**
   - Schema diagrams
   - Table relationships
   - Column descriptions
   - Index information
   - Migration history

3. **Code Documentation**
   - PHPDoc blocks
   - Class descriptions
   - Method explanations
   - Parameter details
   - Return types

4. **Architecture Documentation**
   - System overview
   - Component relationships
   - Design patterns used
   - Data flow diagrams

## Usage

```
/docs-generate
```

Then specify what to document:
- "Generate API documentation"
- "Document UserController endpoints"
- "Create database schema documentation"
- "Generate PHPDoc for app/Services"
- "Document the entire project"

## Documentation Types

### 1. API Documentation (OpenAPI/Swagger)

```yaml
/api/users:
  get:
    summary: List all users
    tags: [Users]
    security:
      - bearerAuth: []
    parameters:
      - name: page
        in: query
        schema:
          type: integer
        description: Page number
    responses:
      200:
        description: Successful response
        content:
          application/json:
            schema:
              type: object
              properties:
                data:
                  type: array
                  items:
                    $ref: '#/components/schemas/User'
      401:
        description: Unauthorized
```

### 2. PHPDoc Comments

```php
/**
 * Create a new user account.
 *
 * This method handles user registration with email verification.
 * It creates a user record and dispatches a verification email.
 *
 * @param StoreUserRequest $request Validated user registration data
 * @return JsonResponse User data with verification status
 * 
 * @throws ValidationException If email already exists
 * @throws \Exception If email sending fails
 * 
 * @example
 * POST /api/users
 * {
 *   "name": "John Doe",
 *   "email": "john@example.com",
 *   "password": "secret123"
 * }
 * 
 * @see \App\Mail\VerifyEmail
 * @see \App\Models\User
 */
public function store(StoreUserRequest $request): JsonResponse
{
    // Implementation
}
```

### 3. Database Schema Documentation

```markdown
# Database Schema

## Users Table

| Column | Type | Nullable | Default | Description |
|--------|------|----------|---------|-------------|
| id | bigint | NO | auto | Primary key |
| name | varchar(255) | NO | - | User's full name |
| email | varchar(255) | NO | - | Unique email address |
| email_verified_at | timestamp | YES | NULL | Email verification timestamp |
| password | varchar(255) | NO | - | Hashed password |
| created_at | timestamp | YES | NULL | Record creation timestamp |
| updated_at | timestamp | YES | NULL | Last update timestamp |

### Indexes
- PRIMARY KEY: `id`
- UNIQUE: `email`
- INDEX: `email_verified_at`

### Relationships
- **Has Many**: Posts (users.id → posts.user_id)
- **Has Many**: Comments (users.id → comments.user_id)
- **Has One**: Profile (users.id → profiles.user_id)

### Foreign Keys
```sql
ALTER TABLE posts 
  ADD CONSTRAINT fk_posts_user_id 
  FOREIGN KEY (user_id) 
  REFERENCES users(id) 
  ON DELETE CASCADE;
```
```

### 4. README Documentation

```markdown
# Project Name

## Overview
Brief description of the project and its purpose.

## Requirements
- PHP 8.3+
- PostgreSQL 15+
- Composer 2.x
- Redis (optional, for caching)

## Installation

1. Clone the repository
   ```bash
   git clone <repository-url>
   cd project-name
   ```

2. Install dependencies
   ```bash
   composer install
   ```

3. Environment setup
   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

4. Database setup
   ```bash
   php artisan migrate
   php artisan db:seed
   ```

## Architecture

### Directory Structure
- `app/Http/Controllers` - API controllers
- `app/Models` - Eloquent models
- `app/Services` - Business logic
- `app/Repositories` - Data access layer

### Design Patterns
- Repository Pattern for data access
- Service Layer for business logic
- API Resources for response transformation
- Form Requests for validation

## API Endpoints

See [API.md](docs/API.md) for complete API documentation.

## Testing

```bash
php artisan test
```

## Deployment

See [DEPLOYMENT.md](docs/DEPLOYMENT.md)
```

### 5. Code Comments

```php
/**
 * User Repository
 * 
 * Handles all database operations related to User model.
 * Implements caching for frequently accessed data.
 * 
 * @package App\Repositories
 */
class UserRepository
{
    /**
     * Find user by email with caching.
     * 
     * Cache TTL: 1 hour
     * Cache key pattern: user:email:{email}
     * 
     * @param string $email User's email address
     * @return User|null User model or null if not found
     */
    public function findByEmail(string $email): ?User
    {
        return Cache::remember(
            "user:email:{$email}",
            3600,
            fn() => User::where('email', $email)->first()
        );
    }
}
```

## Documentation Formats

### Postman Collection
- Import-ready JSON
- Environment variables
- Test scripts
- Example requests

### Swagger/OpenAPI
- Interactive API docs
- Try-it-out feature
- Schema definitions
- Authentication setup

### Markdown Files
- README.md
- API.md
- DEPLOYMENT.md
- CONTRIBUTING.md
- DATABASE.md

## Auto-Generated Sections

1. **Endpoint List**: All routes with methods
2. **Model Relationships**: ER diagram
3. **Permission Matrix**: Role-based access
4. **Configuration Options**: All .env variables
5. **Command List**: Artisan commands
6. **Event/Listener Map**: Event system flow

## Output Structure

```
docs/
├── README.md              # Project overview
├── API.md                 # API endpoints
├── DATABASE.md            # Schema documentation
├── ARCHITECTURE.md        # System design
├── DEPLOYMENT.md          # Deployment guide
├── openapi.yaml           # OpenAPI specification
└── postman_collection.json
```

## Best Practices

- Keep docs close to code (PHPDoc)
- Auto-generate where possible
- Include real examples
- Document edge cases
- Show error responses
- Keep updated with code changes
- Use diagrams for complex flows
