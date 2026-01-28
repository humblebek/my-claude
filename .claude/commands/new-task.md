# New Task Command

Start a new development task with comprehensive analysis.

## Purpose
Analyze the current codebase, identify potential issues, and plan the implementation approach for a new task.

## What This Command Does

1. **Codebase Analysis**
   - Reviews relevant files and directories
   - Identifies existing patterns and conventions
   - Checks for similar implementations
   - Assesses current architecture

2. **Issue Detection**
   - Performance bottlenecks
   - Security vulnerabilities
   - Code smells and anti-patterns
   - Missing validations or error handling
   - Database query optimization opportunities

3. **Task Planning**
   - Break down the task into steps
   - Identify dependencies
   - Suggest implementation approach
   - Highlight potential challenges

## Usage

```
/new-task
```

Then describe your task. For example:
- "Add user authentication with email verification"
- "Create API endpoint for product filtering"
- "Optimize database queries in OrderController"
- "Add soft delete functionality to User model"

## Output

The assistant will provide:
- Current state analysis
- List of issues or improvements needed
- Step-by-step implementation plan
- Relevant Laravel best practices
- Database schema considerations (if applicable)

## Laravel-Specific Checks

- Form Request validation usage
- Repository pattern implementation
- API Resource usage
- Eloquent relationship optimization
- N+1 query detection
- Queue usage for heavy operations
- Cache strategy
- Database transaction usage

## PostgreSQL-Specific Checks

- Index optimization
- Proper use of JSONB columns
- Full-text search implementation
- Partitioning opportunities
- Connection pooling
