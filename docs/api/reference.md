# ArkID API Reference

> **Note**: This API reference is a placeholder and will be implemented during Phase 3 of the ArkID project timeline (Months 10-16).

## Base URLs

- **Production**: `https://arkid.researchark.eu/api/v1`
- **Staging**: `https://staging.arkid.researchark.eu/api/v1`
- **Development**: `https://dev.arkid.researchark.eu/api/v1`

## Authentication

All API requests require authentication using OAuth 2.0/OpenID Connect. Authentication will be handled via bearer tokens:

```
Authorization: Bearer {access_token}
```

## Content Types

- `application/json` for most requests and responses
- `application/problem+json` for error responses (RFC 7807)

## Endpoints Overview

### Idea Endpoints
- `GET /ideas` - List ideas (paginated)
- `GET /ideas/{arkidIdea}` - Retrieve a specific idea
- `POST /ideas` - Create a new idea
- `PUT /ideas/{arkidIdea}` - Update an idea
- `DELETE /ideas/{arkidIdea}` - Delete an idea

### User Endpoints
- `GET /users` - List users (paginated)
- `GET /users/{arkidUser}` - Retrieve a specific user
- `GET /users/{arkidUser}/ideas` - List user's ideas
- `GET /users/{arkidUser}/projects` - List user's projects
- `GET /users/{arkidUser}/organizations` - List user's organizations
- `GET /users/{arkidUser}/products` - List user's products

### Project Endpoints
- `GET /projects` - List projects (paginated)
- `GET /projects/{arkidProject}` - Retrieve a specific project
- `POST /projects` - Create a new project
- `PUT /projects/{arkidProject}` - Update a project
- `DELETE /projects/{arkidProject}` - Delete a project

### Organization Endpoints
- `GET /organizations` - List organizations (paginated)
- `GET /organizations/{arkidOrg}` - Retrieve a specific organization
- `POST /organizations` - Create a new organization
- `PUT /organizations/{arkidOrg}` - Update an organization
- `DELETE /organizations/{arkidOrg}` - Delete an organization

### Product Endpoints
- `GET /products` - List products (paginated)
- `GET /products/{arkidProduct}` - Retrieve a specific product
- `POST /products` - Create a new product
- `PUT /products/{arkidProduct}` - Update a product
- `DELETE /products/{arkidProduct}` - Delete a product

### Relationship Endpoints
- `GET /relationships` - Query relationships between entities
- `POST /relationships` - Create a relationship between entities
- `DELETE /relationships/{relationshipId}` - Delete a relationship

### Verification Endpoints
- `POST /verification/request` - Request verification for an entity
- `PUT /verification/{verificationId}` - Update verification status
- `GET /verification/status` - Check verification status

## Status
Implementation pending. The first version of this API reference will be available with the second pilot deployment in Months 10-12. 