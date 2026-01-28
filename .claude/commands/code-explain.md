# Code Explain Command

Generate detailed explanations of code with Laravel and PHP best practices context.

## Purpose
Provide comprehensive explanations of code functionality, design decisions, and Laravel-specific patterns.

## What This Command Does

1. **Code Analysis**
   - Line-by-line breakdown
   - Identify Laravel conventions used
   - Explain design patterns
   - Highlight PostgreSQL interactions

2. **Context Explanation**
   - Why certain approaches were chosen
   - Trade-offs in implementation
   - Performance implications
   - Security considerations

3. **Related Concepts**
   - Related Laravel features
   - Alternative approaches
   - Best practices
   - Common pitfalls to avoid

## Usage

```
/code-explain
```

Then either:
- Paste code directly
- Reference a file: "Explain app/Http/Controllers/UserController.php"
- Ask about specific functionality: "Explain the authentication flow"

## Explanation Levels

The explanation adapts to:
- **Beginner**: Step-by-step with Laravel fundamentals
- **Intermediate**: Focus on patterns and best practices
- **Advanced**: Architecture decisions and optimization

## What Gets Explained

### Laravel Components
- Controllers and routing
- Eloquent models and relationships
- Middleware and authentication
- Service providers and dependency injection
- Jobs and queues
- Events and listeners
- Form Requests and validation
- API Resources and transformers

### Database Operations
- Query builder usage
- Eloquent ORM patterns
- Raw queries and when to use them
- Transactions and locking
- Migration strategies
- Database optimization techniques

### Architecture Patterns
- Repository pattern
- Service layer
- SOLID principles in Laravel
- Design patterns (Factory, Strategy, Observer, etc.)

## Output Format

- Clear section headers
- Code snippets with annotations
- Performance and security notes
- Links to Laravel documentation (when relevant)
- Practical examples
