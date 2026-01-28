# Azizbek's Laravel Backend Plugin

Claude Code plugin optimized for Laravel/PHP backend development with PostgreSQL database.

## 🚀 Features

### 14 Slash Commands

**Development Commands**
- `/new-task` - Start new task with codebase analysis
- `/code-explain` - Detailed code explanations
- `/code-optimize` - Performance optimization
- `/code-cleanup` - Refactoring and cleanup
- `/feature-plan` - Feature implementation planning
- `/lint` - Code linting and fixes
- `/docs-generate` - Auto documentation generation

**API Commands**
- `/api-new` - Create complete RESTful API
- `/api-test` - Generate comprehensive test suites
- `/api-protect` - Add security and validation

**Database Commands**
- `/migration-new` - Create migrations with indexes
- `/model-new` - Generate Eloquent models
- `/db-optimize` - Database performance optimization
- `/seeder-new` - Create seeders with factories

### 12 AI Agents

**Architecture & Planning**
- `tech-stack-researcher` - Technology choice recommendations
- `system-architect` - Scalable system design
- `backend-architect` - Backend architecture with security
- `requirements-analyst` - Transform ideas to specs

**Code Quality**
- `refactoring-expert` - Systematic refactoring
- `performance-engineer` - Performance optimization
- `security-engineer` - Security assessment
- `laravel-expert` - Laravel best practices

**Database**
- `database-architect` - PostgreSQL schema design

**Documentation & Learning**
- `technical-writer` - Professional documentation
- `learning-guide` - Teaching concepts
- `deep-research-agent` - Comprehensive research

## 📦 Installation

### From GitHub

```bash
# Add marketplace
/plugin marketplace add yourusername/azizbek-laravel-backend-plugin

# Install plugin
/plugin install azizbek-laravel-backend-plugin
```

### From Local Directory

```bash
# Clone repository
git clone https://github.com/yourusername/azizbek-laravel-backend-plugin.git

# Add as local marketplace
/plugin marketplace add /path/to/azizbek-laravel-backend-plugin

# Install
/plugin install azizbek-laravel-backend-plugin
```

## 💡 Usage Examples

### Create Complete API

```
/api-new

Claude: What API would you like to create?
You: Create Product CRUD API with categories
```

Output:
- Migration with proper indexes
- Eloquent model with relationships
- Controller with all CRUD methods
- Form Requests for validation
- API Resources for responses
- Policies for authorization
- Factory for testing
- Feature tests

### Optimize Performance

```
/code-optimize

Then paste your controller method or describe the issue:
"Optimize the product listing query - it's loading all categories separately"
```

### Plan New Feature

```
/feature-plan

Describe: "Add multi-language support for products"
```

Output:
- Database schema design
- Implementation steps
- Laravel architecture
- Migration code
- Testing approach

### Generate Documentation

```
/docs-generate

"Generate API documentation for ProductController"
```

Output:
- OpenAPI/Swagger spec
- PHPDoc comments
- API endpoints list
- Request/Response examples

## 🎯 Optimized For

- **Backend**: Laravel 10+, PHP 8.3+
- **Database**: PostgreSQL 15+
- **Features**: 
  - RESTful APIs
  - Authentication (Sanctum)
  - Queue jobs
  - Cache strategies
  - Full-text search
  - JSONB columns

## 🔧 Tech Stack Support

**Primary Stack:**
- Laravel Framework
- PHP 8.3+
- PostgreSQL
- Redis (caching/queues)

**Features Included:**
- Eloquent ORM best practices
- Repository pattern
- Service layer architecture
- API Resources
- Form Request validation
- Policy-based authorization
- Factory and Seeder generation
- Feature testing

## 📖 Command Quick Reference

| Command | Purpose |
|---------|---------|
| `/new-task` | Analyze code and plan implementation |
| `/api-new` | Scaffold complete RESTful API |
| `/api-protect` | Add security and validation |
| `/migration-new` | Create database migration |
| `/model-new` | Generate Eloquent model |
| `/db-optimize` | Optimize database performance |
| `/feature-plan` | Plan feature implementation |
| `/code-optimize` | Improve code performance |
| `/docs-generate` | Create documentation |

## 🤖 AI Agents

Agents automatically activate based on your questions:

- **Architecture questions** → `system-architect`, `backend-architect`
- **Database questions** → `database-architect`
- **Security concerns** → `security-engineer`
- **Performance issues** → `performance-engineer`
- **Learning questions** → `learning-guide`
- **Tech decisions** → `tech-stack-researcher`

## 🎓 Best Practices Included

✅ Type-safe code with PHP 8.3 features
✅ SOLID principles
✅ Repository pattern
✅ Service layer for business logic
✅ API Resources for responses
✅ Form Requests for validation
✅ Policy-based authorization
✅ N+1 query prevention
✅ Database indexing strategies
✅ Security best practices (OWASP)
✅ PostgreSQL-specific optimizations
✅ Comprehensive testing

## 📋 Project Structure

```
.claude-plugin/
  plugin.json              # Plugin manifest

.claude/
  commands/
    new-task.md           # Development commands
    api-new.md            # API generation
    migration-new.md      # Database commands
    ...
  agents/
    backend-architect.md  # Architecture agents
    database-architect.md # Database experts
    ...
```

## 🔄 Updates

To update the plugin:

```bash
cd /path/to/plugin
git pull origin main

# Reinstall
/plugin install azizbek-laravel-backend-plugin
```

## 🤝 Contributing

1. Fork the repository
2. Create feature branch
3. Add your commands or agents
4. Submit pull request

## 📝 License

MIT License - Use freely in your projects

## 👤 Author

Created by Azizbek for Laravel backend development.

## 🙏 Acknowledgments

Inspired by Edmund's Claude Code plugin structure.

---

**Note**: This plugin is optimized for Laravel + PostgreSQL backend development. Commands emphasize security, performance, and Laravel best practices.
