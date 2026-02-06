# Quick Setup - Get Started in 2 Minutes! ⚡

## Step 1: Install (30 seconds)

```bash
npm install
```

## Step 2: Test Connection (30 seconds)

```bash
npm test
```

You should see:
```
✓ Authenticated successfully
✓ Retrieved communities
✓ Retrieved domains
✓ Retrieved assets
✓ Retrieved asset details
✓ Retrieved attributes
✓ GraphQL endpoint test passed
✅ All tests passed!
```

If any test fails, check:
- Your credentials in `config.json`
- Your network connection
- Your user permissions in Collibra

## Step 3: Your First Export (30 seconds)

```bash
npm start
```

1. **Choose GraphQL** (press Enter)

2. **Choose Community** (press Enter)

3. **Enter a community name** 
   - Example: "Oracle HR Cloud"
   - Must be exact name (case-sensitive)
   - Get names from Test 3 output above

4. **Configure options** (press Enter for defaults)
   - Include attributes? **Yes**
   - Include relations? **Yes**
   - Include responsibilities? **Yes**
   - Output directory? **./exports**

5. **Confirm** (press Enter)

6. **Done!** Your export is in `./exports/`

```
✓ Export completed successfully!
📊 Statistics:
   - Domains: 5
   - Assets: 147
   - With Attributes: 147
   - With Responsibilities: 89
   - With Relations: 134
📁 Output file: ./exports/Oracle_HR_Cloud_GraphQL_2024-02-06T15-30-00.json
```

## Step 4: View Your Export (30 seconds)

```bash
npm run view exports/Oracle_HR_Cloud_GraphQL_2024-02-06T15-30-00.json
```

You'll see a nice summary table showing:
- Community information
- Domains and asset counts
- Asset types breakdown
- Statistics

## That's It! 🎉

You've just exported an entire Collibra community in under 2 minutes.

## What's Next?

### Export Another Community

Just run `npm start` again and enter a different name!

### Export a Single Domain

```bash
npm start
# Choose GraphQL
# Choose Domain
# Enter domain name (e.g., "HR")
```

### Automate Exports

Create a script:

```javascript
// export-daily.js
const CollibraClient = require('./collibraClient');
const CollibraGraphQLExporter = require('./collibraGraphQLExporter');

const run = async () => {
  const client = new CollibraClient();
  client.loadConfig();
  await client.authenticate();
  
  const exporter = new CollibraGraphQLExporter(client);
  
  await exporter.exportCommunityByName('Oracle HR Cloud', {
    includeAttributes: true,
    includeRelations: true,
    includeResponsibilities: true,
    outputDir: './daily-exports'
  });
};

run();
```

Then run it:
```bash
node export-daily.js
```

Or schedule it with cron/Task Scheduler!

### Browse Communities (REST API)

If you want to see all available communities:

```bash
npm run start:rest
# Choose "Select specific communities"
# Browse the list
# Select and export
```

Slower but shows you everything available.

## Common Questions

**Q: How do I find the exact community name?**

A: Run `npm test` and look at Test 3 output, or use REST API mode to browse.

**Q: Can I export multiple communities at once?**

A: For GraphQL, run the script multiple times (or use REST API bulk export).

**Q: The export is taking a long time**

A: Make sure you chose GraphQL, not REST API. GraphQL should be <10 seconds for most communities.

**Q: I get "Community not found" error**

A: Check spelling and capitalization. Community names are case-sensitive.

**Q: Can I filter which assets to export?**

A: Currently exports all assets in a community/domain. You can filter the JSON after export, or modify the GraphQL query in the code.

## Performance Tips

**Fastest Export (Basic data only):**
```bash
npm start
# GraphQL
# Community
# Enter name
# Attributes: Yes
# Relations: No
# Responsibilities: No
```

**Complete Export (Everything):**
```bash
npm start
# GraphQL
# Community
# Enter name
# All options: Yes
```

**Large Communities (>1000 assets):**

Exports work fine, just take a bit longer (~30-60 seconds instead of 5-10).

## Troubleshooting

**"Authentication failed"**
- Check username/password in config.json
- Make sure you can login to Collibra web UI with same credentials

**"GraphQL errors"**
- Run `npm test` to verify GraphQL endpoint works
- Check community name spelling
- Verify you have access to that community

**"No data in export"**
- You might not have access to that community
- Try a different community
- Check permissions in Collibra

## Need Help?

1. **Read the guides:**
   - `README.md` - Overview
   - `GRAPHQL_GUIDE.md` - Detailed GraphQL info
   - `API_REFERENCE.md` - API details

2. **Run tests:**
   ```bash
   npm test
   ```

3. **Check the output:**
   - Look at the actual export JSON
   - Use `npm run view <filename>`

4. **Try REST API:**
   ```bash
   npm run start:rest
   ```
   Sometimes easier to debug with interactive mode

## Success Checklist

- ✅ `npm install` completed
- ✅ `npm test` all 8 tests pass
- ✅ `npm start` → GraphQL → Community → Success!
- ✅ Export file in `./exports/` directory
- ✅ Can view export with `npm run view`

If you've got all these ✅, you're good to go!

---

**Pro Tip:** Save your most common exports as scripts (see "Automate Exports" above) so you can re-run them with one command!
