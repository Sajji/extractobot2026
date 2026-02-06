# GraphQL Export Guide

## Why Use GraphQL?

### Performance Comparison

**REST API Method:**
- 1 call to get domains
- 1 call per domain to get assets (N calls)
- 1 call per asset for attributes (M calls)
- 1 call per asset for relations (M calls)
- 1 call per asset for responsibilities (M calls)
- **Total: 1 + N + 3M API calls**

For a community with 5 domains and 100 assets:
- REST API: **~305 API calls** ⏱️ Several minutes

**GraphQL Method:**
- 1-5 calls total (with pagination, ~100 assets per call)
- Gets ALL data in each call (assets, attributes, relations, responsibilities)
- **Total: ~1-5 API calls** ⚡ Seconds

For the same community:
- GraphQL: **~1-2 API calls** ⚡ <10 seconds

### Benefits

✅ **60-100x faster** for typical communities  
✅ **Single query** gets all data at once  
✅ **Automatic pagination** built-in  
✅ **All attribute types** in one response  
✅ **Relations** (incoming + outgoing) included  
✅ **Responsibilities** included by default  
✅ **Less load** on Collibra servers  

## Quick Start

### Option 1: Use the New Combined Interface

```bash
npm start
```

Then select:
1. **GraphQL (Recommended)** when prompted
2. Choose **Community** or **Domain** export
3. Enter the exact name (e.g., "Oracle HR Cloud" or "HR")
4. Configure what to include
5. Confirm and export!

### Option 2: Direct GraphQL Export Script

Create a simple script:

```javascript
const CollibraClient = require('./collibraClient');
const CollibraGraphQLExporter = require('./collibraGraphQLExporter');

const exportMyCommunity = async () => {
  const client = new CollibraClient();
  client.loadConfig();
  await client.authenticate();
  
  const exporter = new CollibraGraphQLExporter(client);
  
  // Export entire community
  await exporter.exportCommunityByName('Oracle HR Cloud', {
    includeAttributes: true,
    includeRelations: true,
    includeResponsibilities: true,
    outputDir: './exports'
  });
};

exportMyCommunity();
```

### Option 3: Export Just One Domain

```javascript
await exporter.exportDomainByName('HR', {
  includeAttributes: true,
  includeRelations: true,
  includeResponsibilities: true
});
```

## GraphQL Query Structure

The GraphQL exporter builds queries like this:

```graphql
{
  assets(
    limit: 100,
    offset: null,
    where: { 
      domain: { 
        parent: { 
          name: { eq: "Oracle HR Cloud" } 
        } 
      } 
    }
  ) {
    id
    name
    displayName
    type { name }
    status { name }
    tags { name }
    
    domain {
      id
      name
      parent {
        id
        name
      }
    }
    
    # All attribute types
    stringAttributes { type { name } stringValue }
    booleanAttributes { type { name } booleanValue }
    numericAttributes { type { name } numericValue }
    dateAttributes { type { name } dateValue }
    multiValueAttributes { type { name } stringValues }
    
    # Responsibilities
    responsibilities {
      role { name }
      user { fullName userName }
    }
    
    # Relations
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

## Filtering Options

### By Community Name

```javascript
const whereClause = {
  domain: {
    parent: {
      name: { eq: "Oracle HR Cloud" }
    }
  }
};
```

### By Domain Name

```javascript
const whereClause = {
  domain: {
    name: { eq: "HR" }
  }
};
```

### By Asset Type

```javascript
const whereClause = {
  type: {
    name: { eq: "Data Set" }
  }
};
```

### Multiple Conditions

```javascript
const whereClause = {
  domain: {
    parent: {
      name: { eq: "Oracle HR Cloud" }
    }
  },
  type: {
    name: { in: ["Data Set", "Column"] }
  }
};
```

## Output Format

GraphQL exports produce the same JSON structure as REST exports, but with additional metadata:

```json
{
  "community": {
    "name": "Oracle HR Cloud",
    "exportedAt": "2024-02-06T...",
    "method": "GraphQL"
  },
  "domains": [
    {
      "id": "...",
      "name": "HR",
      "assets": [
        {
          "id": "...",
          "name": "EMPLOYEES",
          "displayName": "EMPLOYEES",
          "type": "Table",
          "status": "Implemented",
          "domain": { "id": "...", "name": "HR" },
          "community": { "id": "...", "name": "Oracle HR Cloud" },
          "tags": ["PII", "Sensitive"],
          "attributes": [
            {
              "type": "Description",
              "value": "Employee master table",
              "dataType": "string"
            },
            {
              "type": "Row Count",
              "value": 15000,
              "dataType": "numeric"
            }
          ],
          "responsibilities": [
            {
              "role": "Data Steward",
              "userName": "john.doe",
              "fullName": "John Doe"
            }
          ],
          "relations": [
            {
              "direction": "outgoing",
              "relationType": "contains",
              "relationTypeReverse": "is part of",
              "relatedAsset": {
                "id": "...",
                "displayName": "EMPLOYEE_ID",
                "type": "Column"
              }
            }
          ]
        }
      ]
    }
  ],
  "statistics": {
    "totalDomains": 5,
    "totalAssets": 147,
    "assetsWithAttributes": 147,
    "assetsWithResponsibilities": 89,
    "assetsWithRelations": 134
  }
}
```

## When to Use REST vs GraphQL

### Use GraphQL When:
- ✅ Exporting entire communities (fastest)
- ✅ Need all attributes, relations, AND responsibilities
- ✅ Have many assets (>100)
- ✅ Want to minimize API calls
- ✅ Need the fastest possible export
- ✅ Know the exact community or domain name

### Use REST When:
- ✅ Need to browse available communities first
- ✅ Want to export multiple communities at once
- ✅ Only need basic asset info (no attributes/relations)
- ✅ Need to select from a list interactively
- ✅ Prefer the traditional menu-driven interface

## Troubleshooting

### GraphQL Errors

If you get GraphQL errors, they'll look like this:

```json
{
  "errors": [
    {
      "message": "Community not found",
      "path": ["assets"],
      "extensions": { ... }
    }
  ]
}
```

**Common Issues:**

1. **Community/Domain name not exact**
   - GraphQL uses exact matching with `eq` operator
   - Check spelling and capitalization
   - Use REST API first to see exact names

2. **No access to community**
   - Verify permissions in Collibra
   - Try a different community you have access to

3. **Query timeout**
   - Reduce batch size: `batchSize: 50`
   - Export specific domains instead of entire community

### Performance Tuning

**Large Communities (>1000 assets):**

```javascript
await exporter.exportCommunityByName('Large Community', {
  includeAttributes: true,
  includeRelations: false,  // Skip relations to speed up
  includeResponsibilities: false,
  batchSize: 50  // Smaller batches
});
```

**Maximum Performance:**

```javascript
await exporter.exportCommunityByName('My Community', {
  includeAttributes: true,
  includeRelations: false,
  includeResponsibilities: false,
  batchSize: 100
});
```

## Testing GraphQL

Run the connection test to verify GraphQL is working:

```bash
npm test
```

Look for **Test 8: GraphQL endpoint** - it should pass and show sample data.

## Custom Queries

You can also build custom GraphQL queries:

```javascript
const CollibraClient = require('./collibraClient');

const customQuery = async () => {
  const client = new CollibraClient();
  client.loadConfig();
  await client.authenticate();
  
  const query = `{
    assets(
      limit: 10,
      where: { 
        type: { name: { eq: "Data Set" } }
      }
    ) {
      displayName
      type { name }
      stringAttributes {
        type { name }
        stringValue
      }
    }
  }`;
  
  const response = await client.makeGraphQLRequest(query);
  console.log(JSON.stringify(response.data, null, 2));
};

customQuery();
```

## Next Steps

1. **Test your connection:**
   ```bash
   npm test
   ```

2. **Try a small export:**
   ```bash
   npm start
   # Choose GraphQL
   # Export a domain with <50 assets
   ```

3. **Scale up:**
   - Export full communities
   - Enable all options (attributes, relations, responsibilities)
   - Compare timing with REST API

4. **Automate:**
   - Create scheduled export scripts
   - Build data pipelines
   - Integrate with your data warehouse

## Support

- GraphQL Schema: See `graphqlSchema.json`
- Sample Response: See `graphResponse.json`
- REST API fallback: Use `index.js` instead of `indexGraphQL.js`

---

**Pro Tip:** Start with GraphQL for 90% of use cases. Only use REST when you need the interactive community browser or are exporting multiple unrelated communities at once.
