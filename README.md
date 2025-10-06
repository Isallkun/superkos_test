## SuperKos Developer Interview – Faishal Ridha

Minimal Express.js services that implement:

- Users endpoint: `GET /users`
- External fetch with error handling: `GET /posts`
- Middleware-validated creation: `POST /create-user`

### Prerequisites

- Node.js 16+ and npm

### Install dependencies

```bash
npm init -y
npm install express axios
```

### Project layout

```
app.js          # GET /users
fetchData.js    # GET /posts (uses axios, with error handling)
middleware.js   # POST /create-user (with validation middleware)
```

Note: Each file creates and starts its own Express app on port 3000. Run only one at a time, or change ports if you want them running concurrently.

### Run one of the servers

```bash
# Option A: Users API
node app.js

# Option B: External fetch API
node fetchData.js

# Option C: Create-user API with validation
node middleware.js
```

### Endpoints

#### 1) GET /users (from `app.js`)

Returns a list of users.

Request:

```bash
curl -i http://localhost:3000/users
```

Success response (200):

```json
{
  "success": true,
  "data": [
    { "id": 1, "name": "Alice", "email": "alice@example.com" },
    { "id": 2, "name": "Bob", "email": "bob@example.com" },
    { "id": 3, "name": "Charlie", "email": "charlie@example.com" }
  ]
}
```

Empty list response (404):

```json
{ "success": false, "message": "No users found" }
```

#### 2) GET /posts (from `fetchData.js`)

Fetches posts from `https://jsonplaceholder.typicode.com/posts` via axios with error handling.

Request:

```bash
curl -i http://localhost:3000/posts
```

Success response (200):

```json
{
  "success": true,
  "data": [
    /* array of posts */
  ]
}
```

Possible error responses:

- Network error → 503 Service Unavailable

```json
{ "success": false, "message": "Service unavailable. Please try again later." }
```

- Upstream HTTP error (e.g., 404/500) → mirrors upstream status with message

```json
{ "success": false, "message": "Failed to fetch data: <StatusText>" }
```

- Unexpected error → 500 Internal Server Error

#### 3) POST /create-user (from `middleware.js`)

Validates `name`, `email`, and `password` in JSON body.

Request:

```bash
curl -i -X POST http://localhost:3000/create-user \
  -H "Content-Type: application/json" \
  -d '{"name":"Faishal","email":"faishal@example.com","password":"supersecure"}'
```

Success response (201):

```json
{
  "success": true,
  "message": "User created successfully!",
  "data": { "name": "Faishal", "email": "faishal@example.com" }
}
```

Validation errors (400):

```json
{ "success": false, "message": "Missing required fields: name, email, or password." }
```

```json
{ "success": false, "message": "Invalid email format." }
```

```json
{ "success": false, "message": "Password must be at least 6 characters long." }
```

### Notes

- To run multiple services at the same time, change the `PORT` in each file.
- Ensure `express.json()` is used when working with JSON bodies (already included in `middleware.js`).
