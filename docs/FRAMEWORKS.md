# Framework Support

code-surgeon auto-detects and provides framework-specific guidance for **35+ frameworks**.

## Supported Frameworks

### Frontend
- **React** - Hooks, components, state management
- **Vue** - Composition API, single-file components
- **Angular** - Services, dependency injection
- **Svelte** - Reactive components, stores
- **Next.js** - Server/client components, routing
- **Nuxt** - Vue framework, SSR
- **Gatsby** - Static site generation
- **Astro** - Islands architecture
- **Qwik** - Resumability framework

### Backend
- **Node.js** - JavaScript runtime
- **Express** - Web framework, middleware
- **Django** - Python web framework, ORM
- **FastAPI** - Modern Python API framework
- **Flask** - Lightweight Python framework
- **Rails** - Ruby web framework
- **Spring Boot** - Java framework
- **Laravel** - PHP web framework
- **Go** - Built-in http, gorilla/mux
- **Rust** - Actix, Axum frameworks
- **Python** - Built-in, async frameworks

### Mobile
- **React Native** - Cross-platform mobile
- **Flutter** - Dart-based mobile framework
- **Swift** - iOS native development
- **Kotlin** - Android native development
- **Expo** - React Native tooling

### Other
- **TypeScript** - Language, type system
- **Monorepos** - Lerna, Turborepo, yarn workspaces
- **Testing** - Jest, Vitest, pytest, RSpec
- **Build Tools** - Webpack, Vite, esbuild, Rollup

---

## How It Works

### 1. Automatic Detection

code-surgeon scans:
- `package.json` (npm/yarn/pnpm)
- `pyproject.toml` (Python)
- `go.mod` (Go)
- `Gemfile` (Ruby)
- `Cargo.toml` (Rust)
- `pom.xml` (Maven)
- `build.gradle` (Gradle)
- And 10+ other package managers

### 2. Version Detection

Identifies specific versions:
```json
{
  "react": "18.2.0",
  "typescript": "5.3.0",
  "express": "4.18.2"
}
```

### 3. Pattern Application

Generates framework-specific surgical prompts:

**React Example:**
```markdown
## Task: Create UserProfile Component

### Context
Project uses React 18 with TypeScript.
Pattern: Functional components with hooks.

### Scope
Create component: src/components/UserProfile.tsx
Integrate with: Redux store, React Router

### Approach
1. Define TypeScript interface for props
2. Use useEffect for data fetching
3. Use useCallback for event handlers
4. Use React.memo for optimization

### Example Pattern
function UserProfile({ userId }: Props) {
  const user = useSelector(selectUser(userId));
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUser(userId));
  }, [userId, dispatch]);

  return (...)
}
```

**Django Example:**
```markdown
## Task: Create User API Endpoint

### Context
Project uses Django 4.2 with Django REST Framework.
Pattern: ViewSets with serializers.

### Scope
Create: api/views.py, api/serializers.py
Models: User (existing)

### Approach
1. Create UserSerializer with ModelSerializer
2. Create UserViewSet with CRUD actions
3. Register in router.py
4. Add permissions: IsAuthenticated

### Example Pattern
class UserViewSet(viewsets.ModelViewSet):
  queryset = User.objects.all()
  serializer_class = UserSerializer
  permission_classes = [IsAuthenticated]

  def get_queryset(self):
    return self.queryset.filter(id=self.request.user.id)
```

---

## Framework-Specific Guidance

### React
**Patterns:**
- Functional components with hooks
- Custom hooks for reusable logic
- Context API or Redux for state
- React.memo for performance
- useCallback/useMemo for optimization

**Anti-Patterns:**
- Creating new objects in render
- Missing dependencies in useEffect
- Inline function definitions
- Unnecessary re-renders

### Django
**Patterns:**
- Models → Serializers → ViewSets
- Class-based views over function-based
- Middleware for cross-cutting concerns
- Celery for async tasks
- Signals for event handling

**Anti-Patterns:**
- N+1 queries
- Business logic in views
- Synchronous long-running operations
- Missing select_related/prefetch_related

### Express
**Patterns:**
- Middleware-based architecture
- Separation of routes, controllers, services
- Error handling middleware
- Authentication middleware
- Request validation

**Anti-Patterns:**
- Logic in route handlers
- Missing error handling
- Synchronous blocking operations
- Global state mutation

### Vue
**Patterns:**
- Composition API (preferred)
- Script setup syntax
- Composables for logic reuse
- Pinia or Vuex for state
- Template expressions

**Anti-Patterns:**
- Options API (when Composition available)
- Complex template logic
- Prop mutation
- Missing key in lists

### TypeScript
**Patterns:**
- Strict mode enabled
- Type inference over explicit types
- Union types for flexibility
- Generics for reusability
- Exhaustiveness checking

**Anti-Patterns:**
- `any` type usage
- Non-strict mode
- Over-generification
- Ignoring compiler warnings

---

## Monorepo Support

code-surgeon detects and handles:
- **npm workspaces** - Native npm support
- **yarn workspaces** - Yarn monorepo setup
- **Lerna** - Monorepo management
- **Turborepo** - Build system for monorepos
- **pnpm workspaces** - Fast package manager

**Behavior:**
```
Detected: Monorepo (Turborepo)
├─ packages/ui (React component library)
├─ packages/api (Express backend)
├─ packages/shared (Common utilities)
└─ apps/web (Next.js application)

Analyzes: Cross-package dependencies
Generates: Tasks that respect package boundaries
```

---

## Multi-Language Projects

code-surgeon handles projects with multiple languages:

```
Project Structure:
├─ backend/ (Node.js/Express)
├─ frontend/ (React/TypeScript)
├─ mobile/ (React Native)
├─ scripts/ (Python)
└─ docs/ (Markdown)

code-surgeon:
1. Detects each language
2. Applies framework-specific patterns
3. Considers inter-service dependencies
4. Generates language-appropriate prompts
```

---

## Custom Framework Configuration

If your framework isn't auto-detected:

1. **Mention it in the requirement:**
   ```bash
   /code-surgeon "Add authentication (using Fastify framework)"
   ```

2. **Or in team guidelines:**
   ```markdown
   # Team Guidelines

   ## Framework: Fastify
   - Use plugins for modularity
   - Decorators for dependency injection
   - Hooks for middleware
   ```

code-surgeon will incorporate your guidance.

---

## Framework Best Practices

### When Adding Features
- Follow framework conventions
- Use framework-provided utilities
- Respect framework patterns
- Leverage framework tooling

### Framework Updates
- Test with latest version
- Check for breaking changes
- Review migration guides
- Update team guidelines

### Framework Selection
code-surgeon respects your tech stack:
- Doesn't suggest framework changes
- Works with your existing choices
- Provides guidance within framework constraints
- Suggests framework-specific optimizations

---

## Common Framework Questions

**Q: Can code-surgeon handle framework X?**
A: If it's in the supported list or has package.json/requirements.txt, likely yes. If not, mention it in your requirement.

**Q: Does it work with framework versions?**
A: Yes! Detects versions and provides version-specific guidance (React 16 vs 18, Django 3 vs 4, etc.)

**Q: Can I mix frameworks?**
A: Yes! Monorepos with multiple frameworks are supported. Each service gets appropriate guidance.

**Q: What if my framework isn't detected?**
A: Mention it explicitly: `/code-surgeon "Feature X using [framework] framework"`

---

See [README.md](../README.md) for complete feature list and [USAGE.md](USAGE.md) for command examples.
