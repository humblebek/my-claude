# Feature Plan Command

Create comprehensive implementation plans for new features with Laravel architecture in mind.

## Purpose
Transform feature ideas into actionable implementation plans with clear steps, database design, and architecture decisions.

## What This Command Does

1. **Requirements Analysis**
   - Understand feature scope
   - Identify dependencies
   - Define acceptance criteria
   - List constraints and limitations

2. **Architecture Planning**
   - Database schema design
   - API endpoint structure
   - Service layer organization
   - Event and listener planning
   - Queue job requirements

3. **Implementation Roadmap**
   - Step-by-step task breakdown
   - File structure and organization
   - Testing strategy
   - Deployment considerations

4. **Risk Assessment**
   - Technical challenges
   - Performance implications
   - Security considerations
   - Scalability concerns

## Usage

```
/feature-plan
```

Then describe your feature:
- "Plan a multi-tenant system for the application"
- "Design a notification system with multiple channels"
- "Plan product inventory management with reservations"
- "Create a role-based permission system"

## Plan Structure

### 1. Feature Overview
- Description
- Business value
- User stories
- Success criteria

### 2. Database Design
```sql
-- Tables needed
-- Relationships
-- Indexes
-- Constraints
```

### 3. Laravel Architecture

**Models**
- What models to create
- Relationships to define
- Scopes and accessors needed

**Controllers**
- API endpoints
- Request validation
- Response formatting

**Services**
- Business logic location
- External API integrations
- Complex operations

**Jobs & Queues**
- Async operations
- Background processing
- Scheduled tasks

**Events & Listeners**
- Event triggers
- Listener actions
- Notification dispatch

### 4. Implementation Steps

**Phase 1: Foundation**
1. Create migrations
2. Generate models
3. Define relationships
4. Create seeders

**Phase 2: Core Logic**
1. Implement services
2. Create Form Requests
3. Build controllers
4. Create API Resources

**Phase 3: Additional Features**
1. Add middleware
2. Implement events
3. Create jobs
4. Set up caching

**Phase 4: Testing & Polish**
1. Write feature tests
2. Add API documentation
3. Performance testing
4. Security review

### 5. PostgreSQL Considerations
- JSONB usage for flexible data
- Full-text search setup
- Partitioning strategy (if needed)
- Index optimization
- Materialized views (if needed)

### 6. API Design

```
POST   /api/resource        # Create
GET    /api/resource        # List
GET    /api/resource/{id}   # Show
PUT    /api/resource/{id}   # Update
DELETE /api/resource/{id}   # Delete
```

### 7. Security Checklist
- [ ] Input validation
- [ ] Authorization policies
- [ ] SQL injection prevention
- [ ] XSS protection
- [ ] CSRF tokens
- [ ] Rate limiting
- [ ] Audit logging

### 8. Performance Strategy
- Caching approach
- Database query optimization
- Pagination strategy
- Eager loading plan

### 9. Testing Approach
- Unit tests for services
- Feature tests for API
- Database factories
- Mock external services

### 10. Documentation Needs
- API endpoints
- Request/Response examples
- Database schema
- Setup instructions

## Example Feature Plan Output

For "Multi-language content system":

```
DATABASE SCHEMA:
- translations table (id, translatable_type, translatable_id, locale, field, value)
- Index on (translatable_type, translatable_id, locale, field)

MODELS:
- Translation.php (polymorphic model)
- HasTranslations trait for models

SERVICES:
- TranslationService.php (handle CRUD operations)

API ENDPOINTS:
GET    /api/{resource}/{id}/translations
POST   /api/{resource}/{id}/translations
PUT    /api/{resource}/{id}/translations/{locale}
DELETE /api/{resource}/{id}/translations/{locale}

MIDDLEWARE:
- SetLocale.php (detect and set language)

IMPLEMENTATION STEPS:
1. Create translation migration
2. Create Translation model
3. Create HasTranslations trait
4. Implement TranslationService
5. Create TranslationController
6. Add middleware
7. Write tests
```

## Output Format

Clear, actionable plan with:
- Visual hierarchy
- Code examples
- SQL schemas
- File paths
- Configuration examples
- Testing scenarios
