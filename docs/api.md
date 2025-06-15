# LibrOpen API Documentation


This document provides an overview of the LibrOpen API, including its structure, endpoints, and usage.
It is intended for developers who want to integrate with the LibrOpen platform or build applications using its services.


## Overview
The LibrOpen API is designed to provide access to various functionalities of the LibrOpen platform.
It allows developers to interact with the system, retrieve data, and perform operations programmatically.
The API is built using AdonisJS and follows RESTful principles, making it easy to use and integrate with other systems.

## Base URL
The base URL for the LibrOpen API is:
```
https://api.libropen.com/api
```

## Authentication
The LibrOpen API uses bearer token authentication.
To access protected endpoints, you must include a valid token in the `Authorization` header of your requests:
```Authorization
Bearer YOUR_ACCESS_TOKEN
```

### How to get an access token

To obtain an access token, you need to authenticate a user via the get API token endpoint. After successful authentication, the server will return a token that you can use for subsequent requests.

#### What if I don't have a user account?

If you don't have a user account, you need to create one first through the frontend application or by using the user registration endpoint. This is necessary to obtain an access token for API usage and to ensure that you cannot create users through an automated process.

### Request example :

```bash
curl -X POST https://api.libropen.com/api/users/token \
-H "Content-Type: application/json" \
-d '{
  "email": "youremail@libropen.com",
  "password": "yourpassword"
}'
```
### Response example:
```json
{
  "type": "Bearer",
    "token": "your_access_token",
    "expires_at": "2023-10-01T12:00:00Z"
}
```

## Endpoints
The LibrOpen API provides various endpoints for different functionalities. Below is a list of the main endpoints available:

## 📦 Projects

<details>
<summary><code>GET /projects</code> – List projects</summary>

```bash
curl -X GET "https://api.libropen.com/api/projects?verificationStatus=approved&limit=0&categoryId=2"
```

</details>

<details>
<summary><code>GET /projects/:id</code> – Get a specific project</summary>

```bash
curl -X GET https://api.libropen.com/api/projects/3
```

</details>

<details>
<summary><code>POST /projects</code> – Create a new project</summary>

```bash
curl -X POST https://api.libropen.com/api/projects \
-H "Content-Type: application/json" \
-d '{
  "name": "My Intra",
  "description": "Epitech mobile app",
  "tags": "open-source, web, javascript",
  "gitLink": "https://github.com/user/project",
  "showcaseUrl": "https://project.com",
  "categories": [1]
}'
```

</details>

<details>
<summary><code>PATCH /projects/:id</code> – Update a project</summary>

```bash
curl -X PATCH https://api.libropen.com/api/projects/2 \
-H "Content-Type: application/json" \
-d '{ "name": "Updated Project Name" }'
```

</details>

<details>
<summary><code>DELETE /projects/:id</code> – Delete a project</summary>

```bash
curl -X DELETE https://api.libropen.com/api/projects/1
```

</details>

---

## 📝 Reviews

<details>
<summary><code>POST /reviews/</code> – Submit a review</summary>

```bash
curl -X POST https://api.libropen.com/api/reviews/ \
-H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
-H "Content-Type: application/json" \
-d '{
  "title": "Great project!",
  "body": "Very useful.",
  "rating": 5,
  "projectId": 2
}'
```

</details>

<details>
<summary><code>PATCH /reviews/:id</code> – Update a review</summary>

```bash
curl -X PATCH https://api.libropen.com/api/reviews/123 \
-H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
-H "Content-Type: application/json" \
-d '{ "rating": 3 }'
```

</details>

<details>
<summary><code>DELETE /reviews/:id</code> – Delete a review</summary>

```bash
curl -X DELETE https://api.libropen.com/api/reviews/123 \
-H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

</details>

---

## 👤 Users

<details>
<summary><code>POST /users</code> – Register a new user</summary>

```bash
curl -X POST https://api.libropen.com/api/users \
-H "Content-Type: application/json" \
-d '{
  "fullName": "Martin",
  "username": "Mxrtin",
  "email": "martin@example.com",
  "password": "Test1234",
  "password_confirmation": "Test1234"
}'
```

</details>

<details>
<summary><code>GET /users/me</code> – Get current authenticated user</summary>

```bash
curl -X GET https://api.libropen.com/api/users/me \
-H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

</details>

<details>
<summary><code>PATCH /users/password</code> – Change user password</summary>

```bash
curl -X PATCH https://api.libropen.com/api/users/password \
-H "Content-Type: application/json" \
-d '{
  "email": "user@example.com",
  "oldPassword": "oldpass",
  "newPassword": "newpass",
  "newPassword_confirmation": "newpass"
}'
```

</details>

<details>
<summary><code>GET /users/liked-projects</code> – Get user's liked projects</summary>

```bash
curl -X GET https://api.libropen.com/api/users/liked-projects \
-H "Authorization: Bearer YOUR_ACCESS_TOKEN"
```

</details>

---

## Rate Limiting

The LibrOpen API implements rate limiting to ensure fair usage and protect against abuse.
The default rate limit is set to 3600 requests per day per user.
If you exceed this limit, you will receive a `429 Too Many Requests` response.
If you need a higher rate limit, please contact the LibrOpen team to discuss your requirements (e.g., for bots or automated systems).

## Error Handling

The API uses standard HTTP status codes to indicate the success or failure of requests. Common status codes include:

- `200 OK`: The request was successful.
- `201 Created`: A new resource was successfully created.
- `204 No Content`: The request was successful, but there is no content to return.
- `400 Bad Request`: The request was invalid or malformed.
- `401 Unauthorized`: Authentication is required or has failed.
- `403 Forbidden`: The request is valid, but the user does not have permission to access the resource.
- `404 Not Found`: The requested resource could not be found.
- `429 Too Many Requests`: The user has exceeded the rate limit.
- `500 Internal Server Error`: An unexpected error occurred on the server.

When an error occurs, the API will return a JSON response with an `error` field containing a description of the error.


## Service Status

If you encounter issues with the API, you can check the service status at:
```
https://status.libropen.com
```

Please check this page before reporting any issues, as it may provide information about ongoing incidents or maintenance that could affect the API's availability.

## Support

If you have any questions or need assistance with the LibrOpen API, you can reach out to the support team via the following channels:

- **Discord**: Join the LibrOpen community on Discord for real-time support and discussions.  (<https://discord.gg/9U8KvnUev5>)
- 
- **Email**: You can also send an email to <contact@libropen.com>
