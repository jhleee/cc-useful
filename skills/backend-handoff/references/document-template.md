# Backend-Frontend Integration Document Template

This template defines the exact output structure. Generate each section based on backend code analysis.
Replace all `{placeholder}` values with actual data. Omit sections that don't apply.

---

# Backend-Frontend Integration Spec

> Generated from: {repository_name}
> Backend stack: {detected_framework_and_language}
> Generated at: {timestamp}

---

## 1. Base Configuration

| Key | Value |
|-----|-------|
| Base URL | `{base_url}` |
| API Prefix | `{api_prefix}` |
| Content-Type | `application/json` |
| Auth Method | `{auth_method}` |

---

## 2. Authentication

### Auth Type: {type}

**Token Acquisition:**
- Endpoint: `{auth_endpoint}`
- Method: `{method}`
- Request Body:
  ```json
  {request_body_example}
  ```
- Response:
  ```json
  {response_body_example}
  ```

**Token Usage:**
- Header: `{header_name}: {header_format}`
- Expiry: `{expiry_info}`
- Refresh: `{refresh_mechanism}`

**Token Lifecycle:**
| Event | Action |
|-------|--------|
| Login success | Store token → {storage_recommendation} |
| 401 response | Attempt refresh → redirect to login on failure |
| Logout | Clear token → call revocation endpoint if exists |

---

## 3. API Endpoints

### {Resource Group Name}

#### {METHOD} {path}

| Property | Value |
|----------|-------|
| Summary | {one_line_description} |
| Auth Required | {yes/no} |
| Rate Limit | {if_applicable} |

**Path Parameters:**
| Name | Type | Required | Description |
|------|------|----------|-------------|
| {name} | {type} | {yes/no} | {description} |

**Query Parameters:**
| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| {name} | {type} | {yes/no} | {default} | {description} |

**Request Body:**
```json
{request_body_example}
```

**Response 200:**
```json
{response_body_example}
```

**Error Responses:**
| Status | Code | Description |
|--------|------|-------------|
| {status} | {error_code} | {description} |

---

## 4. Data Models

### {ModelName}

```typescript
interface {ModelName} {
  {field}: {type}; // {description}
}
```

**Field Constraints:**
| Field | Type | Required | Constraints | Notes |
|-------|------|----------|-------------|-------|
| {field} | {type} | {yes/no} | {min/max/pattern/enum} | {notes} |

**Relationships:**
- {field} → references `{RelatedModel}.{field}`

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
| 401 | UNAUTHORIZED | Missing/invalid token | Redirect to login |
| 403 | FORBIDDEN | Insufficient permissions | Show permission denied UI |
| 404 | NOT_FOUND | Resource not found | Show 404 page or fallback |
| 409 | CONFLICT | Duplicate/conflict | Show conflict resolution UI |
| 422 | UNPROCESSABLE | Business logic error | Display `message` to user |
| 429 | RATE_LIMITED | Too many requests | Implement backoff, show retry message |
| 500 | INTERNAL_ERROR | Server error | Show generic error, log for monitoring |

### Validation Error Detail Format

```json
{
  "status": 400,
  "code": "VALIDATION_ERROR",
  "message": "Validation failed",
  "details": {
    "email": ["Invalid email format"],
    "password": ["Must be at least 8 characters"]
  }
}
```

---

## 6. Pagination Pattern

### Pattern: {offset | cursor | page}

**Request:**
| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| {param} | {type} | {default} | {description} |

**Response Envelope:**
```typescript
interface PaginatedResponse<T> {
  data: T[];
  pagination: {
    total: number;
    // additional pagination fields as needed (e.g., nextCursor, page, limit)
  };
}
```

---

## 7. Implementation Checklist

Frontend agent: execute in order.

- [ ] Set up API client with base URL and auth header injection
- [ ] Implement token storage and refresh logic
- [ ] Create TypeScript interfaces from Section 4
- [ ] Implement API call functions for each endpoint in Section 3
- [ ] Add global error interceptor based on Section 5
- [ ] Implement pagination utility based on Section 6
- [ ] Wire up to UI components

---

## Template Usage Notes

- TypeScript interfaces should be generated even if backend is not TypeScript — derive from response shapes.
- Every endpoint must include at least one request and response example with realistic data.
- If backend lacks explicit error codes, infer from middleware/error handlers.
- The Error Code Reference table in Section 5 serves as a default. Override with actual error codes found in backend code.
- Section 7 checklist items should be adjusted based on which sections are included in the final document.
