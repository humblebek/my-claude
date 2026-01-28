# Quick Start Guide

## Installation (2 minutes)

```bash
# Step 1: Add marketplace
/plugin marketplace add yourusername/azizbek-laravel-backend-plugin

# Step 2: Install
/plugin install azizbek-laravel-backend-plugin
```

## First Steps

### 1. Create Your First API (5 minutes)

```
/api-new

Describe: "Product API with name, price, stock, and category relationship"
```

**What you get:**
- ✅ Migration with indexes
- ✅ Eloquent model
- ✅ Controller with CRUD
- ✅ Form Requests
- ✅ API Resources
- ✅ Tests

### 2. Optimize Existing Code

```
/code-optimize

Paste your controller method or describe:
"My product listing is slow, loading 1000+ products"
```

**What you get:**
- Performance analysis
- Optimized code
- Before/After comparison
- Implementation steps

### 3. Plan a Feature

```
/feature-plan

Describe: "Add search functionality with filters by category, price range, and availability"
```

**What you get:**
- Database schema
- Implementation steps
- Code examples
- Testing strategy

## Common Workflows

### Starting New Task

```bash
# 1. Analyze current state
/new-task
"Add authentication to admin panel"

# 2. Create necessary components
/api-new
"Admin authentication endpoints"

# 3. Add security
/api-protect
"Secure admin routes with role-based access"

# 4. Generate tests
/api-test
"Test admin authentication flow"
```

### Database Work

```bash
# 1. Create migration
/migration-new
"Create orders table with user and product relationships"

# 2. Generate model
/model-new
"Order model with user, items, and payment relationships"

# 3. Optimize queries
/db-optimize
"Optimize order queries in OrderController"

# 4. Create seeders
/seeder-new
"Seed 100 orders with items"
```

### Code Quality

```bash
# 1. Cleanup code
/code-cleanup
"Refactor UserController"

# 2. Add linting
/lint
"Check ProductController for issues"

# 3. Generate docs
/docs-generate
"Document all API endpoints"
```

## Using AI Agents

Agents activate automatically when you ask questions:

```
You: "Should I use Redis or database for caching?"
→ tech-stack-researcher agent activates

You: "How do I design a scalable order system?"
→ system-architect agent activates

You: "This query is slow, how to fix?"
→ performance-engineer agent activates
```

## Pro Tips

### 1. Be Specific
❌ "Create an API"
✅ "Create Product CRUD API with category relationship and stock management"

### 2. Provide Context
```
/code-optimize

Context: Laravel 10, PostgreSQL, 10k products
Issue: Product search is slow
Code: [paste your search method]
```

### 3. Combine Commands

```bash
# Complete workflow
/api-new          # Generate API
/api-protect      # Add security
/api-test         # Generate tests
/docs-generate    # Document it
```

### 4. Ask Questions

Don't hesitate to ask agents:
- "What's the best way to handle file uploads?"
- "Should I use Eloquent or Query Builder here?"
- "How do I implement full-text search in PostgreSQL?"

## Examples by Use Case

### E-commerce Backend

```bash
/migration-new "products, categories, orders, order_items tables"
/model-new "Product with category, images, and variants"
/api-new "Complete product catalog API with filtering"
/feature-plan "Shopping cart with session persistence"
```

### Content Management

```bash
/migration-new "posts, authors, categories, tags with many-to-many"
/model-new "Post with soft deletes and publishing workflow"
/api-new "Blog API with drafts, published, and archived states"
/feature-plan "Content versioning system"
```

### User Management

```bash
/api-new "User CRUD with roles and permissions"
/api-protect "Add authentication and authorization"
/feature-plan "Email verification and password reset flow"
```

## Troubleshooting

### Plugin Not Loading

```bash
# Reinstall
/plugin uninstall azizbek-laravel-backend-plugin
/plugin install azizbek-laravel-backend-plugin
```

### Commands Not Working

Check plugin version:
```bash
/plugin list
```

Update if needed:
```bash
cd /path/to/plugin
git pull
/plugin install azizbek-laravel-backend-plugin
```

### Need Help?

1. Check command documentation in `.claude/commands/`
2. Ask an agent for guidance
3. Open an issue on GitHub

## Next Steps

1. ⭐ Star the repository
2. 🔧 Customize commands for your needs
3. 🤝 Share your improvements
4. 📖 Read full documentation

## Resources

- [Laravel Documentation](https://laravel.com/docs)
- [PostgreSQL Documentation](https://www.postgresql.org/docs/)
- [Plugin GitHub Repo](#)

---

Happy coding! 🚀
