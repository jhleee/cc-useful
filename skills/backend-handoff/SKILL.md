---
name: backend-handoff
description: >
  This skill should be used when the user asks to "create integration docs",
  "generate API spec for frontend", "hand off backend to frontend",
  "prepare API contract for frontend team", "create API handoff document",
  "document backend APIs for frontend agent", "backend-frontend handoff",
  or when backend implementation is complete and needs to be documented
  for a frontend AI agent to consume. Not for standalone OpenAPI/Swagger
  generation or non-REST API documentation.
---

# Backend-Frontend Integration Document Generator

Generate a structured integration document from backend code analysis.
The output targets a frontend AI agent as its reader — not a human developer.
The document must be goal-oriented, unambiguous, and immediately actionable.

## Workflow

### Phase 1: Backend Code Discovery

Scan the codebase to locate backend source files. Search in order:

1. **Route/Controller definitions** — entry points for API endpoints
   - Glob patterns: `**/routes/**`, `**/controllers/**`, `**/api/**`, `**/*.controller.*`, `**/*.route.*`, `**/*.router.*`
2. **Model/Schema definitions** — data structures and validation rules
   - Glob patterns: `**/models/**`, `**/schemas/**`, `**/entities/**`, `**/types/**`, `**/*.model.*`, `**/*.schema.*`, `**/*.entity.*`
3. **Middleware** — auth, error handling, validation
   - Glob patterns: `**/middleware/**`, `**/middlewares/**`, `**/guards/**`, `**/interceptors/**`
4. **Configuration** — base URL, CORS, rate limits
   - Files: `**/config/**`, `.env.example`, `**/app.*`, `**/main.*`, `**/server.*`
5. **OpenAPI/Swagger specs** — if present, use as primary source
   - Files: `**/swagger.*`, `**/openapi.*`, `**/*.swagger.*`, `**/api-docs*`

Read each discovered file. Extract:
- HTTP method + path for each endpoint
- Request parameters (path, query, body) with types
- Response shapes with status codes
- Authentication requirements per endpoint
- Validation rules and constraints
- Error response patterns

### Phase 2: User Feedback Request

Before generating the document, present analysis results to the user for confirmation.

Use AskUserQuestion to verify:

1. **Scope** — "Discovered {N} endpoints across {M} resource groups. Proceed with all, or limit scope?"
2. **Auth details** — "Detected {auth_type} authentication. Confirm token header format and storage recommendation."
3. **Missing info** — Flag any endpoints where request/response types could not be fully inferred.

Do not proceed to Phase 3 until user confirms.

### Phase 3: Document Generation

Generate a Markdown document following the template in `references/document-template.md`.

**Project root resolution:** Nearest ancestor directory containing `.git`, or current working directory as fallback.

**Output rules:**
- Write to `{project_root}/FRONTEND_INTEGRATION.md` unless user specifies otherwise
- Include only sections relevant to the analyzed backend (omit empty sections)
- Generate TypeScript interfaces for all data models regardless of backend language
- Include realistic example payloads derived from actual model schemas
- Every endpoint must have at least one request/response example

**Section generation order:**
1. Base Configuration — extract from server config, env files
2. Authentication — analyze auth middleware, token handling
3. API Endpoints — group by resource, document each endpoint fully
4. Data Models — convert all models to TypeScript interfaces with constraints
5. Error Handling — analyze error middleware, document error response structure
6. Pagination — detect pagination patterns, document request/response format
7. Implementation Checklist — auto-generate based on included sections

### Phase 4: Validation

After generation, verify the document:
- Every discovered endpoint is documented
- All TypeScript interfaces are syntactically valid
- Request/response examples match the declared types
- No `{placeholder}` values remain in the output
- Cross-references between endpoints and models are consistent

## Extraction Patterns by Framework

Framework-agnostic analysis. Common patterns (non-exhaustive — for unlisted frameworks, use the fallback rule below):

| Pattern | Indicator | Extract |
|---------|-----------|---------|
| Express/Koa | `router.get()`, `app.post()` | Method + path from call |
| FastAPI | `@app.get("/path")`, `@router.post` | Decorator args |
| Spring | `@GetMapping`, `@PostMapping` | Annotation values |
| NestJS | `@Get()`, `@Post()`, `@Controller()` | Decorator args + controller prefix |
| Django REST | `urlpatterns`, `ViewSet` | URL conf + ViewSet actions |
| Rails | `resources :name`, `get "/path"` | routes.rb entries |
| Go (Gin/Chi) | `r.GET()`, `r.Route()` | Method calls |
| ASP.NET | `[HttpGet]`, `[Route]` | Attribute values |

For unrecognized frameworks: infer endpoints from HTTP method keywords and path string literals.

## Output Quality Criteria

The generated document succeeds when a frontend AI agent can:
1. Set up an API client without additional questions
2. Implement every API call with correct types
3. Handle all error cases with appropriate UI responses
4. Implement auth flow from login to token refresh

## Additional Resources

### Reference Files

- **`references/document-template.md`** — Complete output document template with all sections, placeholder formats, and usage notes. Consult when generating the final document.

### Example Files

- **`examples/example-output.md`** — A completed integration document generated from a sample Express + JWT CRUD API. Use as a concrete reference for output quality and format.
