# Visual Community/Domain Selection - No More Typing!

## What Changed

**GraphQL exports now have an interactive visual selection interface!**

No more typing exact community names - just browse and click!

## Before vs After

### Before (Manual Entry)

```
What would you like to export?
→ Community (all domains in a community)

Enter community name (exact match): Landing Zone  ← Had to type exactly!
```

**Problems:**
- ❌ Had to know exact name
- ❌ Had to match capitalization
- ❌ Couldn't see what's available
- ❌ Easy to make typos

### After (Visual Selection)

```
What would you like to export?
→ Community (browse and select from list)

Which communities do you want to see?
→ Root communities only (5)
  All communities including subcommunities (56)

📋 Communities List:

┌───┬────────────────────────────────┬──────────────────────┐
│ # │ Community Name                 │ Description          │
├───┼────────────────────────────────┼──────────────────────┤
│ 1 │ 92. BCBS Demo                  │ Demo environment     │
│ 2 │ Landing Zone                   │ Data landing zone    │
│ 3 │ Finance                        │ Finance department   │
│ 4 │ HR                             │ Human resources      │
│ 5 │ Operations                     │ Operations dept      │
└───┴────────────────────────────────┴──────────────────────┘

Select community to export:
❯ 92. BCBS Demo - Demo environment
  Landing Zone - Data landing zone  ← Just select!
  Finance - Finance department
  HR - Human resources
  Operations - Operations dept

✓ Selected: Landing Zone
ℹ️  Note: This export will include all subcommunities automatically.
```

**Benefits:**
- ✅ See all available communities
- ✅ Browse descriptions
- ✅ No typing required
- ✅ No typos possible
- ✅ Filter by root/all
- ✅ See parent info for subcommunities

## How It Works

### 1. Choose What to Export

```
What would you like to export?
❯ Community (browse and select from list)  ← New text!
  Domain (browse and select from list)      ← New!
  Back to method selection
```

Both communities AND domains now have visual selection!

### 2. Filter Communities (Optional)

If exporting a community, you can filter the list:

```
Which communities do you want to see?
❯ Root communities only (5)                 ← Shorter list
  All communities including subcommunities (56)  ← Everything
```

**Root communities only:**
- Shows only top-level communities (no parent)
- Much shorter list (typically 5-10 items)
- Easier to find main organizational units

**All communities:**
- Shows everything including subcommunities
- Shows parent information: "Dev (child of: Landing Zone)"
- Good if you know you want a specific subcommunity

### 3. Visual Table

The table shows:
- **Number** - For easy reference
- **Community Name** - Full name
- **Description** - What it's for

### 4. Select from List

Use arrow keys to navigate, Enter to select:

```
Select community to export: (Use arrow keys)
  92. BCBS Demo - Demo environment
❯ Landing Zone - Data landing zone  ← Your cursor
  Finance - Finance department
  HR - Human resources
  Operations - Operations dept
```

### 5. Confirmation

Shows what you selected and reminds about subcommunities:

```
✓ Selected: Landing Zone
ℹ️  Note: This export will include all subcommunities automatically.
```

## Domain Selection (New!)

Domains also get visual selection now:

```
What would you like to export?
→ Domain (browse and select from list)

📋 Fetching domains...

Found 147 domains

Select domain to export:
❯ Customer Data - Customer information tables
  Transaction Systems - Payment processing
  Reporting - Analytics and reports
  Risk Management - Risk assessment data
  Compliance - Regulatory compliance
  ...

✓ Selected: Customer Data
```

**Shows:**
- Domain name
- Description (if available)
- Sorted alphabetically
- Up to 1000 domains (most instances have less)

## Features

### Smart Filtering

**Root communities view:**
- Perfect for large Collibra instances
- Shows only organizational top-level
- Typically 5-20 items instead of 50-100

**All communities view:**
- Shows subcommunities with parent info
- Example: "Dev Environment (child of: Landing Zone)"
- Useful if you know the subcommunity name

### Helpful Notes

**For root communities:**
```
ℹ️  Note: This export will include all subcommunities automatically.
```

**For subcommunities:**
```
ℹ️  Note: This export will include any subcommunities of this community.
```

Reminds you that subcommunities are always included!

### Pagination

Lists longer than 15 items are paginated:
- Use arrow keys to scroll
- Type to search/filter
- Shows "Page 1 of 4" at bottom

## Complete Flow Example

```bash
npm start
```

**1. Choose method:**
```
Choose export method:
❯ ⚡ GraphQL (Recommended - Faster, single query per community)
```

**2. Choose what to export:**
```
What would you like to export?
❯ Community (browse and select from list)
```

**3. Filter if needed:**
```
Which communities do you want to see?
❯ Root communities only (5)
```

**4. See the table:**
```
Found 5 root communities

┌───┬────────────────┬─────────────────┐
│ # │ Community Name │ Description     │
├───┼────────────────┼─────────────────┤
│ 1 │ 92. BCBS Demo  │ Demo env        │
│ 2 │ Landing Zone   │ Data landing    │
│ 3 │ Finance        │ Finance dept    │
│ 4 │ HR             │ Human resources │
│ 5 │ Operations     │ Operations      │
└───┴────────────────┴─────────────────┘
```

**5. Select from list:**
```
Select community to export:
❯ Landing Zone - Data landing
```

**6. Confirmation:**
```
✓ Selected: Landing Zone
ℹ️  Note: This export will include all subcommunities automatically.
```

**7. Configure options:**
```
⚙️  Export Options

? Include assets? Yes
? Include all asset attributes? Yes
? Include asset relations? Yes
? Output directory: ./exports
```

**8. Confirm and export:**
```
? Ready to export community "Landing Zone" via GraphQL? Yes

🚀 Starting GraphQL export...
```

**Done!**

## Why This Is Better

### User Experience

| Feature | Manual Entry | Visual Selection |
|---------|-------------|------------------|
| **See what's available** | ❌ No | ✅ Yes |
| **Descriptions** | ❌ No | ✅ Yes |
| **Typo-proof** | ❌ No | ✅ Yes |
| **Case-sensitive** | ❌ Yes | ✅ No issue |
| **Fast to use** | ⏱️ Slow | ⚡ Fast |
| **Root filtering** | ❌ No | ✅ Yes |
| **Learn structure** | ❌ No | ✅ Yes |

### For Large Instances

**If you have 100+ communities:**
- Old way: Scroll through terminal output, copy/paste name carefully
- New way: Browse filtered list of 10 root communities, click

**If you have subcommunities:**
- Old way: How do you even know what subcommunities exist?
- New way: See them in the list with parent info

### For New Users

**First time using the tool:**
- Old way: "What communities do we have? Let me find out..."
- New way: Browse the list, see everything available

**Learning the structure:**
- Old way: Need separate documentation
- New way: The selection screen IS the documentation

## Technical Details

### How It Works

1. **Fetches communities** using REST API (`GET /communities`)
2. **Separates root vs all** based on presence of `parent` field
3. **Shows filtered list** in a table
4. **User selects visually** using inquirer
5. **Gets exact name** from selection
6. **Passes to GraphQL exporter** (same fast export!)

### Performance

**Initial load:**
- Fetches all communities: ~1-2 seconds
- Shows table: Instant
- Selection: Instant

**Export:**
- Same GraphQL speed: ~5-10 seconds
- No slowdown from visual selection!

### Fallback

If community fetching fails:
- Falls back to manual entry
- Shows error message
- User can still type name

## Commands

```bash
# Main app with visual selection
npm start

# View hierarchy (useful reference)
npm run communities

# Old REST API (already had visual selection)
npm run start:rest
```

## Files Updated

- ✅ `indexGraphQL.js` - Added visual selection for communities and domains
- ✅ Uses existing `displayCommunities()` function for table
- ✅ Leverages `inquirer` list prompts
- ✅ Smart filtering by root/all communities

## Try It Now!

```bash
npm start
```

Then:
1. Choose GraphQL
2. Choose Community
3. See the magic! ✨

No more typing, just browsing and clicking!

---

**Status:** ✅ Implemented  
**Applies to:** GraphQL exports (communities and domains)  
**Speed:** Same fast GraphQL export, just easier selection  
**Fallback:** Manual entry still available if needed
