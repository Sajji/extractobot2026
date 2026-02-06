# Subcommunity Support - Automatic Recursive Export

## What's New

**Exporting a community now automatically includes ALL subcommunities!**

When you select a parent community, the exporter will:
1. Find the selected community
2. Recursively find ALL subcommunities (children, grandchildren, etc.)
3. Export assets from ALL of them in a single file

## How It Works

### Community Hierarchy Example

```
92. BCBS Demo (root)
├── 00 - Group (Global Entity)
│   └── Sub-entity A
│       └── Sub-entity B
├── 01 - Retail Banking (Local Entity)
└── Landing Zone
    ├── Dev Environment
    └── Prod Environment
```

**When you export "92. BCBS Demo":**
- ✅ Exports "92. BCBS Demo" 
- ✅ Exports "00 - Group (Global Entity)"
- ✅ Exports "Sub-entity A"
- ✅ Exports "Sub-entity B"
- ✅ Exports "01 - Retail Banking (Local Entity)"
- ✅ Exports "Landing Zone"
- ✅ Exports "Dev Environment"
- ✅ Exports "Prod Environment"

**Total: 8 communities in one export!**

## GraphQL Export with Subcommunities

### Example Run

```bash
npm start
# Choose GraphQL
# Choose Community
# Enter: "Landing Zone"
```

**Output:**
```
📦 Exporting community: Landing Zone (via GraphQL)
──────────────────────────────────────────────────
Looking up community: Landing Zone
✓ Found community: Landing Zone (0194a749-d22a-7c88-9527-32f1342ed365)
Checking for subcommunities...
✓ Found 2 subcommunity(ies):
  - Dev Environment
  - Prod Environment

Exporting assets from 3 communities...

Fetching assets via GraphQL...
  Fetched 147 assets...
✓ Total assets fetched: 147

Transforming assets...

✓ Export completed successfully!
📊 Statistics:
   - Communities: 3 (Landing Zone + 2 subcommunities)
   - Domains: 12
   - Assets: 147
   - With Attributes: 147
   - With Relations: 89
📁 Output file: ./exports/Landing_Zone_GraphQL_2024-02-06T16-30-00.json
```

## Export File Structure

The export JSON now includes subcommunity information:

```json
{
  "community": {
    "name": "Landing Zone",
    "id": "0194a749-d22a-7c88-9527-32f1342ed365",
    "exportedAt": "2024-02-06T16:30:00.000Z",
    "method": "GraphQL",
    "includesSubcommunities": true,
    "subcommunities": [
      {
        "id": "abc123...",
        "name": "Dev Environment"
      },
      {
        "id": "def456...",
        "name": "Prod Environment"
      }
    ]
  },
  "domains": [
    {
      "id": "...",
      "name": "My Domain",
      "community": "Landing Zone",  // Shows which community each domain belongs to
      "assets": [...]
    },
    {
      "id": "...",
      "name": "Another Domain",
      "community": "Dev Environment",  // This domain is from a subcommunity
      "assets": [...]
    }
  ],
  "statistics": {
    "totalCommunities": 3,
    "totalDomains": 12,
    "totalAssets": 147,
    "assetsWithAttributes": 147,
    "assetsWithRelations": 89
  }
}
```

## How Subcommunity Detection Works

### REST API Approach

1. Fetch all communities from Collibra
2. Build a parent-child map
3. Recursively find all descendants:
   ```javascript
   findDescendants(parentId) {
     for each community:
       if community.parent.id === parentId:
         add to descendants
         recursively find children of this community
   }
   ```

### GraphQL Filtering

The WHERE clause uses the `in` operator to filter by multiple community IDs:

```graphql
{
  assets(
    where: {
      domain: {
        parent: {
          id: { in: ["community-id-1", "community-id-2", "community-id-3"] }
        }
      }
    }
  ) {
    # ... fields
  }
}
```

This gets all assets from all specified communities in **one query**!

## REST API Export with Subcommunities

REST API exports also support subcommunities:

```bash
npm run start:rest
# Browse communities
# Select a parent community
# Export
```

**Same behavior:**
- Automatically finds subcommunities
- Exports all of them
- Shows progress for each community
- Marks each domain with its source community

## Root vs Non-Root Communities

### Detecting Root Communities

A community is a **root-level community** if it has **no parent**:

```json
{
  "id": "...",
  "name": "92. BCBS Demo",
  "parent": null  // ← Root community (no parent)
}
```

A community is a **subcommunity** if it has a parent:

```json
{
  "id": "...",
  "name": "Landing Zone",
  "parent": {
    "id": "...",
    "name": "92. BCBS Demo"  // ← Has a parent
  }
}
```

### Export Behavior

**Exporting a root community:**
- Exports the root + ALL nested subcommunities recursively
- Could be 10s or 100s of communities depending on your structure

**Exporting a subcommunity:**
- Exports the subcommunity + its children
- Does NOT export its siblings or parent

## Performance Considerations

### GraphQL (Recommended)

**Advantages:**
- ⚡ One query gets all assets from all communities
- 🚀 Extremely fast regardless of community count
- 📦 Single network round trip

**Example:**
- 10 communities with 500 total assets
- GraphQL: ~5-10 seconds
- REST: Would take 5-10 minutes

### REST API

**With subcommunities:**
- Processes each community sequentially
- Fetches domains for each community
- Fetches assets for each domain
- Much slower for hierarchies

**Recommendation:** Always use GraphQL for parent communities with subcommunities!

## Disabling Subcommunity Export

If you want to export ONLY the selected community (no subcommunities):

**Currently:** Not available in the UI, but can be added if needed.

**Workaround:** Export a subcommunity instead of its parent, or manually filter the JSON after export.

## Use Cases

### 1. Complete Organizational Export

Export your entire organization:
```bash
# Export the root community
npm start
# GraphQL → Community → "My Organization"
# Gets everything!
```

### 2. Department Export

Export a specific department with all sub-departments:
```bash
npm start
# GraphQL → Community → "Finance Department"
# Gets Finance + all sub-departments
```

### 3. Environment Export

Export all environments under a project:
```bash
npm start
# GraphQL → Community → "Customer Portal"
# Gets Dev, Test, Staging, Prod environments
```

## Troubleshooting

### "No subcommunities found" but I know they exist

**Possible causes:**
1. The subcommunities have a different parent than expected
2. Your user doesn't have access to the subcommunities
3. The community structure changed recently

**Solution:** Use REST mode to browse and verify the hierarchy:
```bash
npm run start:rest
# View the communities list
# Check the parent field for each community
```

### Export taking a long time

**Cause:** You selected a root community with many subcommunities.

**Solutions:**
1. Use GraphQL (much faster)
2. Export a specific subcommunity instead
3. Be patient - large hierarchies take time with REST API

### How do I see the community hierarchy?

**Create a script:**
```javascript
const CollibraClient = require('./collibraClient');

const showHierarchy = async () => {
  const client = new CollibraClient();
  client.loadConfig();
  await client.authenticate();
  
  const response = await client.getCommunities();
  const communities = response.results || [];
  
  // Build hierarchy map
  const roots = communities.filter(c => !c.parent);
  
  const printHierarchy = (parentId, indent = 0) => {
    const children = communities.filter(c => c.parent?.id === parentId);
    children.forEach(child => {
      console.log('  '.repeat(indent) + '└─ ' + child.name);
      printHierarchy(child.id, indent + 1);
    });
  };
  
  console.log('Community Hierarchy:\n');
  roots.forEach(root => {
    console.log(root.name);
    printHierarchy(root.id, 1);
  });
};

showHierarchy();
```

## Benefits

✅ **One export, complete data** - No need to export subcommunities separately  
✅ **Preserves context** - Each domain tagged with its community  
✅ **Automatic discovery** - No manual subcommunity listing needed  
✅ **Fast with GraphQL** - Single query handles entire hierarchy  
✅ **Audit trail** - Export file shows which subcommunities were included  

## New Statistics

The export now tracks:
- `totalCommunities` - How many communities were included
- Community list in metadata
- Each domain shows its source community

This makes it easy to:
- Verify completeness
- Understand data distribution
- Track which subcommunities contributed assets

---

**Status:** ✅ Implemented  
**Applies to:** Both GraphQL and REST API exports  
**Default behavior:** Always includes subcommunities  
**Test with:** `npm start` → Select a community with subcommunities
