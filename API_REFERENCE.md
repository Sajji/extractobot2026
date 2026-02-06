# Collibra REST API 2.0 - Quick Reference

This guide provides quick reference for commonly used Collibra REST API endpoints based on the OpenAPI specification.

## Authentication

### Supported Methods
- **Basic Authentication**: Username + Password (Base64 encoded in Authorization header)
- **JWT Authentication**: Bearer token
- **Session Authentication**: POST to /auth/sessions

### Session Login
```http
POST /rest/2.0/auth/sessions
Content-Type: application/json

{
  "username": "your-username",
  "password": "your-password"
}
```

Response includes JSESSIONID cookie for subsequent requests.

## Communities

### Find Communities
```http
GET /rest/2.0/communities?limit=1000&sortField=NAME&sortOrder=ASC&excludeMeta=true
```

**Parameters:**
- `offset`: Starting position (deprecated, use cursor)
- `limit`: Max results (max: 1000, default: 1000)
- `cursor`: Cursor-based pagination
- `name`: Filter by name
- `nameMatchMode`: START, END, ANYWHERE, EXACT (default: ANYWHERE)
- `parentId`: Filter by parent community
- `excludeMeta`: Exclude system communities (default: true)
- `sortField`: NAME, CREATED_BY, CREATED_ON, LAST_MODIFIED, ID
- `sortOrder`: ASC, DESC

**Response:**
```json
{
  "results": [
    {
      "id": "uuid",
      "name": "Community Name",
      "description": "...",
      "type": { "id": "uuid", "name": "..." }
    }
  ],
  "total": 42,
  "offset": 0,
  "limit": 1000
}
```

## Domains

### Find Domains
```http
GET /rest/2.0/domains?communityId={communityId}&limit=1000&excludeMeta=true
```

**Parameters:**
- `offset`: Starting position (deprecated)
- `limit`: Max results (max: 1000)
- `cursor`: Cursor-based pagination
- `name`: Filter by name
- `nameMatchMode`: START, END, ANYWHERE, EXACT
- `communityId`: Filter by community
- `typeId`: Filter by domain type UUID
- `typePublicId`: Filter by domain type public ID
- `excludeMeta`: Exclude system domains (default: true)
- `includeSubCommunities`: Include sub-communities (default: false)

## Assets

### Find Assets
```http
GET /rest/2.0/assets?domainId={domainId}&limit=1000&excludeMeta=true
```

**Parameters:**
- `offset`: Starting position (deprecated)
- `limit`: Max results (max: 1000)
- `cursor`: Cursor-based pagination
- `name`: Filter by name (display name or full name)
- `nameMatchMode`: START, END, ANYWHERE, EXACT
- `domainId`: Filter by domain UUID
- `communityId`: Filter by community UUID
- `typeIds`: Array of asset type UUIDs
- `typePublicIds`: Array of asset type public IDs
- `statusIds`: Array of status UUIDs
- `tagNames`: Array of tag names
- `typeInheritance`: Include subtypes (default: true)
- `excludeMeta`: Exclude system assets (default: true)
- `sortField`: NAME, DISPLAY_NAME, ID
- `sortOrder`: ASC, DESC

### Get Asset by ID
```http
GET /rest/2.0/assets/{assetId}
```

### Get Asset Attributes
```http
GET /rest/2.0/assets/{assetId}/attributes?offset=0&limit=1000
```

**Response:**
```json
{
  "results": [
    {
      "id": "uuid",
      "type": {
        "id": "uuid",
        "name": "Definition"
      },
      "value": "The attribute value"
    }
  ]
}
```

### Get Asset Relations
```http
GET /rest/2.0/assets/{assetId}/relations?offset=0&limit=1000
```

**Parameters:**
- `offset`: Starting position
- `limit`: Max results (max: 1000)
- `relationTypeId`: Filter by relation type

**Response:**
```json
{
  "results": [
    {
      "id": "uuid",
      "type": {
        "id": "uuid",
        "role": "is parent of",
        "corole": "is child of"
      },
      "source": { "id": "uuid", "name": "..." },
      "target": { "id": "uuid", "name": "..." }
    }
  ]
}
```

### Get Asset Responsibilities
```http
GET /rest/2.0/assets/{assetId}/responsibilities?offset=0&limit=1000
```

**Response:**
```json
{
  "results": [
    {
      "id": "uuid",
      "role": {
        "id": "uuid",
        "name": "Data Steward"
      },
      "user": {
        "id": "uuid",
        "userName": "john.doe",
        "firstName": "John",
        "lastName": "Doe"
      },
      "resourceType": "Asset"
    }
  ]
}
```

## Users

### Find Users
```http
GET /rest/2.0/users?offset=0&limit=1000
```

**Parameters:**
- `offset`: Starting position
- `limit`: Max results (max: 1000)
- `userId`: Array of user IDs
- `name`: Search in name fields
- `nameSearchFields`: Fields to search (default: all)
- `title`: Organizational title
- `emailAddress`: Email address
- `firstName`: First name
- `lastName`: Last name
- `userName`: Username
- `enabled`: Filter by enabled status

## Asset Types

### Find Asset Types
```http
GET /rest/2.0/assetTypes?offset=0&limit=1000
```

## Relation Types

### Find Relation Types
```http
GET /rest/2.0/relationTypes?offset=0&limit=1000
```

## Common Response Structure

Most list endpoints follow this pattern:

```json
{
  "results": [...],      // Array of items
  "total": 123,          // Total count (if counted)
  "offset": 0,           // Current offset
  "limit": 1000,         // Current limit
  "nextCursor": "..."    // Cursor for next page (if using cursor pagination)
}
```

## Error Responses

### 400 Bad Request
```json
{
  "message": "Request data is malformed",
  "field": "fieldName"
}
```

### 401 Unauthorized
Authentication failed or not provided.

### 404 Not Found
Resource not found.

### 500 Internal Server Error
Server error - report to Collibra support.

## Best Practices

1. **Pagination**
   - Use cursor-based pagination for best performance
   - Default limit is 1000, which is also the maximum
   - For large datasets, implement pagination loops

2. **Authentication**
   - Basic Auth is simplest for scripts
   - JWT tokens for API integrations
   - Sessions for web applications

3. **Performance**
   - Use `excludeMeta=true` to skip system-generated items
   - Be specific with filters to reduce result sets
   - Use typeInheritance wisely (disable if only exact type needed)

4. **Error Handling**
   - Always check HTTP status codes
   - Parse error response bodies for details
   - Implement retry logic for 500 errors

## GraphQL Alternative

For complex queries or to reduce API calls, consider using the GraphQL endpoint:

```http
POST /graphql/knowledgeGraph/v1
Content-Type: application/json

{
  "query": "query { assets(limit: 10) { id name type { name } } }",
  "variables": {}
}
```

See graphqlSchema.json for full schema details.

## Rate Limiting

- Collibra may implement rate limiting
- Respect API limits to avoid throttling
- Implement exponential backoff for retries

## References

- Full API Documentation: https://developer.collibra.com/rest/2.0/reference/
- OpenAPI Spec: collibra-rest.json (this directory)
- GraphQL Schema: graphqlSchema.json (this directory)
