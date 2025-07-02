# Setting Up D1 Database with Cloudflare Pages

## Prerequisites
- Cloudflare account
- Wrangler CLI installed (`npm install -g wrangler`)
- Logged into Wrangler (`wrangler login`)

## Step 1: Create Database Schema
Create a file named `schema.sql`:
```sql
CREATE TABLE users (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT NOT NULL,
    email TEXT UNIQUE NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);

-- Add additional tables as needed
```

## Step 2: Create and Deploy Database
```bash
# Create new D1 database
wrangler d1 create my-database --remote

# Deploy schema to database
wrangler d1 execute my-database --remote --file=./schema.sql
```
⚠️ Make sure to save the database_id from the creation output

## Step 3: Set Up Database Bindings
1. Navigate to [Cloudflare Dashboard](https://dash.cloudflare.com)
2. Go to Workers & Pages
3. Select your Pages project
4. Click Settings
5. Navigate to Functions > D1 Database Bindings
6. Click "Add binding"
7. Configure binding:
   - Variable name: `DB` (or your preferred name)
   - Select your database from dropdown

## Step 4: Access Database in Code
```typescript
interface Env {
  DB: D1Database;
}

export default async function handler(req: Request, env: Env) {
  const users = await env.DB.prepare('SELECT * FROM users').all();
  return Response.json(users);
}
```

## Optional: Environment-Specific Bindings
You can set different bindings for production and preview environments in the Pages settings under the respective tabs.

## Common Database Operations
```typescript
// Insert
await env.DB.prepare(
  'INSERT INTO users (name, email) VALUES (?, ?)'
).bind('John', 'john@example.com').run();

// Select
const user = await env.DB.prepare(
  'SELECT * FROM users WHERE id = ?'
).bind(1).first();

// Update
await env.DB.prepare(
  'UPDATE users SET name = ? WHERE id = ?'
).bind('New Name', 1).run();

// Delete
await env.DB.prepare(
  'DELETE FROM users WHERE id = ?'
).bind(1).run();
```

## Best Practices
- Always use prepared statements with parameter binding
- Handle database errors appropriately
- Validate input data before database operations
- Use transactions for multiple related operations

## Troubleshooting
If you encounter issues:
1. Verify database was created successfully
2. Confirm schema was deployed
3. Check binding configuration in Pages settings
4. Ensure environment variables are correctly set

For more information, visit [Cloudflare D1 Documentation](https://developers.cloudflare.com/d1/)
