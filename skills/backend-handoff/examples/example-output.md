# Backend-Frontend Integration Spec

> Generated from: task-manager-api
> Backend stack: Express.js (Node.js 20) + Prisma ORM + PostgreSQL
> Generated at: 2026-02-11

---

## 1. Base Configuration

| Key | Value |
|-----|-------|
| Base URL | `http://localhost:3000` |
| API Prefix | `/api/v1` |
| Content-Type | `application/json` |
| Auth Method | `JWT (Bearer Token)` |

---

## 2. Authentication

### Auth Type: JWT

**Token Acquisition:**
- Endpoint: `POST /api/v1/auth/login`
- Method: `POST`
- Request Body:
  ```json
  {
    "email": "user@example.com",
    "password": "securePassword123"
  }
  ```
- Response:
  ```json
  {
    "accessToken": "eyJhbGciOiJIUzI1NiIs...",
    "refreshToken": "dGhpcyBpcyBhIHJlZnJl...",
    "expiresIn": 3600
  }
  ```

**Token Usage:**
- Header: `Authorization: Bearer {accessToken}`
- Expiry: 1 hour (3600 seconds)
- Refresh: `POST /api/v1/auth/refresh` with `{ "refreshToken": "..." }`

**Token Lifecycle:**
| Event | Action |
|-------|--------|
| Login success | Store accessToken in memory, refreshToken in httpOnly cookie |
| 401 response | Call `/api/v1/auth/refresh` → on failure redirect to `/login` |
| Logout | Call `POST /api/v1/auth/logout` → clear all tokens |

---

## 3. API Endpoints

### Auth

#### POST /api/v1/auth/register

| Property | Value |
|----------|-------|
| Summary | Register a new user account |
| Auth Required | no |
| Rate Limit | 5 requests/minute |

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Response 201:**
```json
{
  "id": "clx1234567890",
  "name": "John Doe",
  "email": "john@example.com",
  "createdAt": "2026-02-11T09:00:00.000Z"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | Invalid email format or password too short |
| 409 | EMAIL_ALREADY_EXISTS | Email already registered |

#### POST /api/v1/auth/login

| Property | Value |
|----------|-------|
| Summary | Authenticate and receive tokens |
| Auth Required | no |
| Rate Limit | 10 requests/minute |

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "securePassword123"
}
```

**Response 200:**
```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIs...",
  "refreshToken": "dGhpcyBpcyBhIHJlZnJl...",
  "expiresIn": 3600
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 401 | INVALID_CREDENTIALS | Wrong email or password |
| 429 | RATE_LIMITED | Too many login attempts |

---

### Tasks

#### GET /api/v1/tasks

| Property | Value |
|----------|-------|
| Summary | List tasks for authenticated user |
| Auth Required | yes |
| Rate Limit | none |

**Query Parameters:**
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| status | string | no | all | Filter: `pending`, `in_progress`, `completed`, `all` |
| page | number | no | 1 | Page number |
| limit | number | no | 20 | Items per page (max 100) |
| sortBy | string | no | createdAt | Sort field: `createdAt`, `dueDate`, `priority` |
| order | string | no | desc | Sort order: `asc`, `desc` |

**Response 200:**
```json
{
  "data": [
    {
      "id": "clx9876543210",
      "title": "Implement login page",
      "description": "Create login form with email/password fields",
      "status": "in_progress",
      "priority": "high",
      "dueDate": "2026-02-15T00:00:00.000Z",
      "assigneeId": "clx1234567890",
      "projectId": "clxproj001",
      "createdAt": "2026-02-10T09:00:00.000Z",
      "updatedAt": "2026-02-11T14:30:00.000Z"
    }
  ],
  "pagination": {
    "total": 42,
    "page": 1,
    "limit": 20,
    "totalPages": 3
  }
}
```

#### POST /api/v1/tasks

| Property | Value |
|----------|-------|
| Summary | Create a new task |
| Auth Required | yes |
| Rate Limit | none |

**Request Body:**
```json
{
  "title": "Design dashboard layout",
  "description": "Create wireframe for the main dashboard",
  "status": "pending",
  "priority": "medium",
  "dueDate": "2026-02-20T00:00:00.000Z",
  "projectId": "clxproj001"
}
```

**Response 201:**
```json
{
  "id": "clxtask999",
  "title": "Design dashboard layout",
  "description": "Create wireframe for the main dashboard",
  "status": "pending",
  "priority": "medium",
  "dueDate": "2026-02-20T00:00:00.000Z",
  "assigneeId": "clx1234567890",
  "projectId": "clxproj001",
  "createdAt": "2026-02-11T15:00:00.000Z",
  "updatedAt": "2026-02-11T15:00:00.000Z"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | Missing required fields or invalid values |
| 404 | PROJECT_NOT_FOUND | Referenced projectId does not exist |

#### PATCH /api/v1/tasks/:id

| Property | Value |
|----------|-------|
| Summary | Update an existing task |
| Auth Required | yes |
| Rate Limit | none |

**Path Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | yes | Task ID (cuid format) |

**Request Body:**
```json
{
  "status": "completed",
  "priority": "low"
}
```

**Response 200:**
```json
{
  "id": "clxtask999",
  "title": "Design dashboard layout",
  "description": "Create wireframe for the main dashboard",
  "status": "completed",
  "priority": "low",
  "dueDate": "2026-02-20T00:00:00.000Z",
  "assigneeId": "clx1234567890",
  "projectId": "clxproj001",
  "createdAt": "2026-02-11T15:00:00.000Z",
  "updatedAt": "2026-02-11T16:30:00.000Z"
}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 400 | VALIDATION_ERROR | Invalid field values |
| 403 | FORBIDDEN | Task belongs to another user |
| 404 | TASK_NOT_FOUND | Task ID does not exist |

#### DELETE /api/v1/tasks/:id

| Property | Value |
|----------|-------|
| Summary | Delete a task |
| Auth Required | yes |
| Rate Limit | none |

**Path Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| id | string | yes | Task ID (cuid format) |

**Response 204:** No content

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| 403 | FORBIDDEN | Task belongs to another user |
| 404 | TASK_NOT_FOUND | Task ID does not exist |

---

## 4. Data Models

### User

```typescript
interface User {
  id: string;         // cuid, primary key
  name: string;       // display name
  email: string;      // unique, login identifier
  createdAt: string;  // ISO 8601 datetime
}
```

**Field Constraints:**
| Field | Type | Required | Constraints | Notes |
|-------|------|----------|-------------|-------|
| id | string | yes | cuid format | Auto-generated |
| name | string | yes | 1-100 chars | |
| email | string | yes | valid email, unique | |
| createdAt | string | yes | ISO 8601 | Auto-generated |

### Task

```typescript
interface Task {
  id: string;            // cuid, primary key
  title: string;         // task title
  description: string;   // task detail (nullable)
  status: TaskStatus;    // current status
  priority: TaskPriority; // priority level
  dueDate: string | null; // ISO 8601 datetime, nullable
  assigneeId: string;    // references User.id
  projectId: string;     // references Project.id
  createdAt: string;     // ISO 8601 datetime
  updatedAt: string;     // ISO 8601 datetime
}

type TaskStatus = "pending" | "in_progress" | "completed";
type TaskPriority = "low" | "medium" | "high" | "urgent";
```

**Field Constraints:**
| Field | Type | Required | Constraints | Notes |
|-------|------|----------|-------------|-------|
| id | string | yes | cuid format | Auto-generated |
| title | string | yes | 1-200 chars | |
| description | string | no | max 5000 chars | Nullable |
| status | TaskStatus | yes | enum: pending, in_progress, completed | Default: pending |
| priority | TaskPriority | yes | enum: low, medium, high, urgent | Default: medium |
| dueDate | string \| null | no | ISO 8601 | Nullable |
| assigneeId | string | yes | cuid format | Auto-set to current user on create |
| projectId | string | yes | cuid format | Must reference existing Project |

**Relationships:**
- assigneeId → references `User.id`
- projectId → references `Project.id`

### AuthTokens

```typescript
interface AuthTokens {
  accessToken: string;   // JWT
  refreshToken: string;  // opaque token
  expiresIn: number;     // seconds until accessToken expires
}
```

---

## 5. Error Handling

### Error Response Structure

```typescript
interface ErrorResponse {
  status: number;
  code: string;
  message: string;
  details?: Record<string, string[]>;
}
```

### Error Code Reference

| HTTP Status | Code | Meaning | Frontend Action |
|-------------|------|---------|-----------------|
| 400 | VALIDATION_ERROR | Invalid input | Display field-level errors from `details` |
| 401 | UNAUTHORIZED | Missing/expired token | Attempt token refresh → redirect to `/login` on failure |
| 401 | INVALID_CREDENTIALS | Wrong email/password | Show "Invalid credentials" message |
| 403 | FORBIDDEN | Not owner of resource | Show "Access denied" message |
| 404 | TASK_NOT_FOUND | Task ID invalid | Show "Task not found" + redirect to task list |
| 404 | PROJECT_NOT_FOUND | Project ID invalid | Show "Project not found" message |
| 409 | EMAIL_ALREADY_EXISTS | Duplicate email on register | Show "Email already in use" message |
| 429 | RATE_LIMITED | Too many requests | Show retry message, implement exponential backoff |
| 500 | INTERNAL_ERROR | Server error | Show generic error message |

### Validation Error Example

```json
{
  "status": 400,
  "code": "VALIDATION_ERROR",
  "message": "Validation failed",
  "details": {
    "title": ["Title is required"],
    "priority": ["Must be one of: low, medium, high, urgent"]
  }
}
```

---

## 6. Pagination Pattern

### Pattern: page-based

**Request:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| page | number | 1 | Page number (1-indexed) |
| limit | number | 20 | Items per page (max 100) |

**Response Envelope:**
```typescript
interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    total: number;
    page: number;
    limit: number;
    totalPages: number;
  };
}
```

---

## 7. Implementation Checklist

Frontend agent: execute in order.

- [ ] Set up API client with base URL `http://localhost:3000/api/v1` and `Authorization: Bearer` header injection
- [ ] Implement token storage (accessToken in memory, refreshToken in httpOnly cookie)
- [ ] Implement 401 interceptor → call `/auth/refresh` → redirect to login on failure
- [ ] Create TypeScript interfaces: `User`, `Task`, `TaskStatus`, `TaskPriority`, `AuthTokens`, `ErrorResponse`, `PaginatedResponse<T>`
- [ ] Implement auth API calls: `register`, `login`, `refresh`, `logout`
- [ ] Implement task API calls: `listTasks`, `createTask`, `updateTask`, `deleteTask`
- [ ] Add global error interceptor mapping error codes to UI actions per Section 5
- [ ] Implement pagination utility using `page`/`limit` parameters and `PaginatedResponse` envelope
- [ ] Wire up to UI components
