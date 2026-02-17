# Users and Roles Export - Full Name Resolution & Inheritance

## What's New

**Responsibilities export is back** - but now with **full user name resolution** and **inheritance tracking**!

Based on your previous MCP server implementation, we've added:
- ✅ **Full user details** (name, email, username)
- ✅ **Inherited responsibilities** from domains and communities  
- ✅ **UserGroup support** with group names
- ✅ **Batch fetching** for efficiency
- ✅ **Categorized by source** (direct, from community, from domain)

## Quick Example

### What You Get Now

```json
{
  "responsibilities": {
    "summary": {
      "total": 5,
      "direct": 2,
      "inherited": 3,
      "fromCommunity": 2,
      "fromDomain": 1
    },
    "direct": [
      {
        "role": "Data Steward",
        "owner": {
          "type": "User",
          "userName": "john.doe",
          "fullName": "John Doe",
          "firstName": "John",
          "lastName": "Doe",
          "emailAddress": "john.doe@company.com"
        },
        "baseResource": "Customer Data"
      }
    ],
    "inherited": {
      "fromCommunity": [
        {
          "role": "Data Owner",
          "owner": {
            "type": "User",
            "userName": "jane.smith",
            "fullName": "Jane Smith",
            "emailAddress": "jane.smith@company.com"
          },
          "baseResource": "Finance",
          "baseResourceType": "Community"
        }
      ],
      "fromDomain": [
        {
          "role": "Data Quality Lead",
          "owner": {
            "type": "UserGroup",
            "name": "Data Governance Team",
            "description": "Team responsible for data governance"
          },
          "baseResource": "Customer 360",
          "baseResourceType": "Domain"
        }
      ]
    }
  }
}
```

## The Problem We Solved

### Before (UUID Hell)

```json
{
  "responsibilities": [
    {
      "role": "Data Owner",
      "user": "a1b2c3d4-e5f6-7890-abcd-ef1234567890"  // ❌ Useless!
    }
  ]
}
```

### After (Human Readable!)

```json
{
  "responsibilities": {
    "direct": [
      {
        "role": "Data Owner",
        "owner": {
          "fullName": "John Doe",  // ✅ Useful!
          "emailAddress": "john.doe@company.com"
        }
      }
    ]
  }
}
```

## How It Works

### REST API Export (Most Detailed)

Uses the enhanced `getAssetResponsibilitiesEnriched()` method:

1. **Fetches responsibilities** with `includeInherited=true`
2. **Extracts unique user/group IDs**
3. **Batch fetches user details**: `/users?userId=id1&userId=id2&userId=id3`
4. **Batch fetches group details**: `/userGroups?userGroupId=id1&userGroupId=id2`
5. **Enriches each responsibility** with full details
6. **Categorizes** by direct vs inherited
7. **Sub-categorizes inherited** by community vs domain

**Performance:**
- 100 assets with 500 total responsibilities
- Uses only **2-3 API calls** (not 500!)
- Batch fetching = super efficient

### GraphQL Export (Simpler, Faster)

Uses GraphQL's built-in responsibilities:

```graphql
responsibilities {
  role { name }
  user {
    userName
    firstName
    lastName
    fullName
    emailAddress
  }
}
```

**Note:** GraphQL doesn't provide inheritance info - only direct responsibilities.

**Best for:** Fast exports where you just need user names, not inheritance tracking.

## Export Options

### Enable Responsibilities

When you run `npm start`, you'll see:

```
⚙️  Export Options

? Include assets? Yes
? Include all asset attributes? Yes
? Include asset relations? Yes
? Include asset responsibilities (users & roles with full names)? (y/N)
```

**Default:** No (opt-in)

**When to enable:**
- ✅ Need to know who owns/stewards assets
- ✅ Need to contact data owners
- ✅ Building access control reports
- ✅ Compliance/audit requirements
- ✅ Understanding governance structure

**When to skip:**
- ❌ Just need technical metadata
- ❌ Want fastest possible export
- ❌ Don't care about ownership

## REST API vs GraphQL

### REST API Responsibilities

**Pros:**
- ✅ Full inheritance tracking
- ✅ Shows where responsibilities come from
- ✅ Categorized by community/domain
- ✅ Same efficient batch fetching

**Cons:**
- ⏱️ Slower than GraphQL (more API calls)
- 📊 More complex output structure

**Use when:**
- Need to understand governance hierarchy
- Want to know inherited vs direct
- Building compliance reports

### GraphQL Responsibilities

**Pros:**
- ⚡ Very fast (single query)
- 📊 Simpler output
- ✅ Full user names included

**Cons:**
- ❌ No inheritance info
- ❌ Only direct responsibilities

**Use when:**
- Just need user names
- Want fastest export
- Don't care about inheritance

## Try It Now

### GraphQL Export (Simpler)

```bash
npm start
# Choose GraphQL
# Choose Community
# Select a community
# When asked about responsibilities: Yes
# Export!
```

### REST API Export (Full Inheritance)

```bash
npm run start:rest
# Choose community
# When asked about responsibilities: Yes
# Export!
```

---

**Status:** ✅ Implemented  
**Based on:** Your MCP server code  
**Performance:** 2-3 API calls regardless of asset count  
**Default:** Off (opt-in via prompt)
