# GraphQL Syntax Error - Fixed! 🔧

## The Problem

You were getting this error:
```
Invalid syntax with offending token '"domain"' at line 2 column 48
```

## Root Cause

GraphQL uses its own syntax for input objects, **not JSON syntax**.

**Wrong (what I had):**
```graphql
where: {"domain": {"parent": {"name": {"eq": "Landing Zone"}}}}
```

**Correct (what it should be):**
```graphql
where: {domain: {parent: {name: {eq: "Landing Zone"}}}}
```

Notice: **No quotes around field names** in GraphQL!

## The Fix

I added a `toGraphQLInputObject()` method that properly converts JavaScript objects to GraphQL syntax:

```javascript
// Converts this JavaScript:
{ domain: { parent: { name: { eq: "Landing Zone" } } } }

// To this GraphQL:
{domain: {parent: {name: {eq: "Landing Zone"}}}}
```

## How to Verify the Fix

### Option 1: Quick GraphQL Test

```bash
npm run test:graphql
```

This will:
1. Build a query for "Landing Zone" 
2. Show you the generated GraphQL syntax
3. Execute the query
4. Show sample results

**What you should see:**
```
✅ Query successful! Retrieved X assets
Sample asset:
  Name: ...
  Domain: ...
  Community: Landing Zone
```

### Option 2: Test Different Community

```bash
node testGraphQL.js "Your Community Name"
```

### Option 3: Full Export Test

```bash
npm start
# Choose GraphQL
# Choose Community
# Enter: Landing Zone
```

Should now work! ✅

## Understanding the Query

The exporter builds queries like this:

```graphql
{
  assets(
    limit: 100, 
    offset: null, 
    where: {domain: {parent: {name: {eq: "Landing Zone"}}}}
  ) {
    id
    name
    displayName
    domain {
      id
      name
      parent {
        id
        name
      }
    }
    type { name }
    status { name }
    tags { name }
    stringAttributes {
      type { name }
      stringValue
    }
    # ... more attribute types
    responsibilities {
      role { name }
      user { fullName userName }
    }
    outgoingRelations {
      target { id displayName type { name } }
      type { role corole }
    }
    incomingRelations {
      source { id displayName type { name } }
      type { role corole }
    }
  }
}
```

## Common GraphQL Syntax Rules

1. **Field names**: No quotes
   - ✅ `domain: {name: "HR"}`
   - ❌ `"domain": {"name": "HR"}`

2. **String values**: Must have quotes
   - ✅ `{eq: "Landing Zone"}`
   - ❌ `{eq: Landing Zone}`

3. **Numbers**: No quotes
   - ✅ `limit: 100`
   - ❌ `limit: "100"`

4. **Booleans**: No quotes
   - ✅ `includeAll: true`
   - ❌ `includeAll: "true"`

5. **Null**: No quotes
   - ✅ `offset: null`
   - ❌ `offset: "null"`

6. **Arrays**: Use square brackets
   - ✅ `typeIds: ["id1", "id2"]`
   - ❌ `typeIds: "id1,id2"`

## Debugging Tips

If you still get syntax errors:

1. **Run the GraphQL test:**
   ```bash
   npm run test:graphql
   ```
   This shows the exact query being generated.

2. **Check the query output:**
   The exporter now prints the first 500 chars of the query on the first call.

3. **Verify your community name:**
   Community names are case-sensitive and must be exact.
   ```bash
   # List available communities
   npm run start:rest
   # Then choose to browse communities
   ```

4. **Try a simple query:**
   Use the test script to verify basic connectivity:
   ```bash
   node testGraphQL.js
   ```

## What Changed in the Code

**collibraGraphQLExporter.js:**
- ✅ Added `toGraphQLInputObject()` method
- ✅ Replaced `JSON.stringify()` with GraphQL formatter
- ✅ Added debug logging to show generated queries

**testGraphQL.js (NEW):**
- Tests GraphQL query generation
- Shows before/after syntax conversion
- Executes test query
- Verifies results

**package.json:**
- ✅ Added `npm run test:graphql` command

## Next Steps

1. **Test the fix:**
   ```bash
   npm run test:graphql
   ```

2. **Try your export again:**
   ```bash
   npm start
   # GraphQL → Community → Landing Zone
   ```

3. **Should complete in seconds!** ⚡

## Still Having Issues?

If you still get errors:

1. **Share the output from:**
   ```bash
   npm run test:graphql "Landing Zone"
   ```

2. **Check if the community exists:**
   - Login to Collibra web UI
   - Verify "Landing Zone" exists
   - Check exact spelling/capitalization

3. **Try a different community:**
   Use REST mode to browse and find community names:
   ```bash
   npm run start:rest
   ```

## Technical Details

The `toGraphQLInputObject()` method recursively converts JavaScript objects:

- **Objects** → `{key: value, key2: value2}`
- **Arrays** → `[item1, item2]`
- **Strings** → `"value"`
- **Numbers** → `123`
- **Booleans** → `true` / `false`
- **Null** → `null`

This ensures proper GraphQL syntax for all WHERE clauses, regardless of complexity.

---

**Status:** ✅ Fixed  
**Test Command:** `npm run test:graphql`  
**Ready to Use:** Yes! Try your export again.
