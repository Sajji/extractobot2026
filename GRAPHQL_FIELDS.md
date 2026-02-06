# Collibra GraphQL Schema - Field Reference

## Common Mistakes ❌ → Correct Usage ✅

Based on actual GraphQL schema testing with your Collibra instance.

### Asset Fields

| ❌ Wrong | ✅ Correct | Notes |
|----------|-----------|-------|
| `name` | `displayName` | Assets don't have a `name` field |
| `asset.name` | `asset.displayName` | Only displayName exists |

**Asset structure:**
```graphql
{
  assets {
    id              # ✅ Available
    displayName     # ✅ Use this instead of 'name'
    type { name }   # ✅ Asset type name
    status { name } # ✅ Status name
    tags { name }   # ✅ Tag names
    
    # NOT available:
    # name          # ❌ Does not exist
  }
}
```

### Domain & Community Fields

Domains and communities **do** have a `name` field:

```graphql
{
  assets {
    domain {
      id          # ✅ Available
      name        # ✅ Domain has 'name'
      parent {    # This is the community
        id        # ✅ Available
        name      # ✅ Community has 'name'
      }
    }
  }
}
```

### Relation Fields

Relations reference other assets, which only have `displayName`:

```graphql
{
  assets {
    outgoingRelations {
      target {
        id              # ✅ Available
        displayName     # ✅ Use this (not 'name')
        type { name }   # ✅ Asset type name
      }
      type {
        source { name } # ✅ Relation type source
        role            # ✅ Role name
        target { name } # ✅ Relation type target
        corole          # ✅ Corole name
      }
    }
    incomingRelations {
      source {
        id              # ✅ Available
        displayName     # ✅ Use this (not 'name')
        type { name }   # ✅ Asset type name
      }
      # ... same type structure
    }
  }
}
```

### Attribute Fields

All attribute types follow the same pattern:

```graphql
{
  assets {
    stringAttributes {
      type { name }      # ✅ Attribute type name
      stringValue        # ✅ The actual value
    }
    booleanAttributes {
      type { name }
      booleanValue       # ✅ Boolean value
    }
    numericAttributes {
      type { name }
      numericValue       # ✅ Numeric value
    }
    dateAttributes {
      type { name }
      dateValue          # ✅ Date value
    }
    multiValueAttributes {
      type { name }
      stringValues       # ✅ Array of strings
    }
  }
}
```

### Responsibility Fields

```graphql
{
  assets {
    responsibilities {
      role { name }      # ✅ Role name (e.g., "Data Steward")
      user {
        fullName         # ✅ User's full name
        userName         # ✅ Username
      }
    }
  }
}
```

## Complete Working Query

This is the structure the exporter now uses:

```graphql
{
  assets(
    limit: 100,
    offset: null,
    where: {domain: {parent: {name: {eq: "Your Community"}}}}
  ) {
    # Asset basic info
    id
    displayName         # NOT 'name'
    type { name }
    status { name }
    tags { name }
    
    # Location
    domain {
      id
      name              # Domains have 'name'
      parent {          # Community
        id
        name            # Communities have 'name'
      }
    }
    
    # All attribute types
    stringAttributes {
      type { name }
      stringValue
    }
    booleanAttributes {
      type { name }
      booleanValue
    }
    numericAttributes {
      type { name }
      numericValue
    }
    dateAttributes {
      type { name }
      dateValue
    }
    multiValueAttributes {
      type { name }
      stringValues
    }
    
    # Responsibilities
    responsibilities {
      role { name }
      user {
        fullName
        userName
      }
    }
    
    # Relations
    outgoingRelations {
      target {
        id
        displayName     # NOT 'name'
        type { name }
      }
      type {
        source { name }
        role
        target { name }
        corole
      }
    }
    incomingRelations {
      source {
        id
        displayName     # NOT 'name'
        type { name }
      }
      type {
        source { name }
        role
        target { name }
        corole
      }
    }
  }
}
```

## Field Name Summary

**Objects with `name` field:**
- ✅ Domain
- ✅ Community (domain.parent)
- ✅ Type (asset.type, attribute.type, etc.)
- ✅ Status
- ✅ Tag
- ✅ Role
- ✅ Relation types (source, target)

**Objects with `displayName` field:**
- ✅ Asset
- ✅ Related assets (in relations)

**Objects with both name fields:**
- ✅ User has `fullName` and `userName`

## Testing Your Queries

Use the test script to verify field availability:

```bash
npm run test:graphql
```

This will show:
1. The generated query
2. Any field errors
3. Sample data returned

## Common Error Messages

### "Field 'name' in type 'Asset' is undefined"
**Fix:** Change `asset.name` to `asset.displayName`

### "Field 'name' in type 'RelatedAsset' is undefined"
**Fix:** In relations, use `target.displayName` or `source.displayName`

### "Field 'displayName' in type 'Domain' is undefined"
**Fix:** Domains use `name`, not `displayName`

## Why This Matters

The REST API and GraphQL API use **different field names** for assets:

| API | Asset Name Field |
|-----|------------------|
| REST API | `name` and `displayName` both exist |
| GraphQL | Only `displayName` exists |

This is why the exporter needed to be updated!

## Verification

After updating the exporter:

1. **Run GraphQL test:**
   ```bash
   npm run test:graphql
   ```
   Should succeed with no field errors.

2. **Check the query:**
   Look at "4. Full GraphQL query" in test output.
   Verify it uses `displayName` not `name` for assets.

3. **Try export:**
   ```bash
   npm start
   ```
   Should complete successfully.

---

**Status:** ✅ Fixed in collibraGraphQLExporter.js  
**Test:** `npm run test:graphql`
