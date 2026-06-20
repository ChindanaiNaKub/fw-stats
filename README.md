# fw-stats

CLI tool + dashboard for FastWork marketplace stats.

Reads **public** category/subcategory data (minimum prices, average ratings, review counts) from FastWork's public GraphQL endpoint. No login required. No private data accessed.

```
npm install -g fw-stats
fw-stats
fw-stats --json
fw-stats --csv --top 50
fw-stats --sort reviews
fw-stats serve   # local dashboard on port 3456
```

## Usage

```bash
fw-stats [options]

Commands:
  serve          Start local dashboard server on port 3456

Options:
  --sort <field>   Sort by: price | ratio (default) | reviews
  --top <N>        Show only top N results (default: 20)
  --all            Show all results (no limit)
  --json           Output JSON array
  --csv            Output CSV with headers
  --tsv            Output TSV (tab-separated)
  -h, --help       Show this help
```

Examples:

```bash
# Top 20 subcategories by opportunity score (price × reviews)
fw-stats

# Top 10 highest-priced subcategories as JSON
fw-stats --sort price --top 10 --json

# Full data as CSV
fw-stats --all --csv > fw-stats.csv

# Launch web dashboard
fw-stats serve
```

## Dashboard

`fw-stats serve` starts a local web dashboard at `http://localhost:3456` with:
- Sortable table of all categories/subcategories
- Bar chart: Top 15 subcategories by review count
- Bar chart: Subcategories per category
- Refresh button to fetch latest data

## Data

This tool fetches **public marketplace listing data**:
- Category / subcategory names
- Minimum price per subcategory
- Average rating
- Review count

No user account data, no private information, no personal data is ever accessed.

## License

MIT
