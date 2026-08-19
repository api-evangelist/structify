---
name: Structify
description: Use when building data pipelines, extracting structured data from PDFs and websites, designing schemas for datasets, enriching data with web information, or automating data workflows. Agents should reach for this skill when users need to transform unstructured data into queryable datasets, scrape web content, process documents, or create AI-powered data pipelines.
metadata:
    mintlify-proj: structify
    version: "1.0"
---

# Structify Skill

## Product Summary

Structify is an AI-powered data platform that transforms unstructured information (PDFs, websites, documents) into structured, queryable datasets. Use it to build data pipelines with natural language prompts, extract information from web pages and documents, design custom schemas, and enrich datasets with web data. The platform combines web scraping, PDF extraction, and AI-powered data enrichment into a single workflow.

**Key files and commands:**
- Python SDK: `pip install structifyai`
- API base: `https://api.structify.ai`
- Environment variable: `STRUCTIFY_API_TOKEN`
- Dashboard: `https://app.structify.ai`
- Primary docs: https://docs.structify.ai

## When to Use

Reach for Structify when:
- A user needs to extract structured data from PDFs, documents, or websites
- Building a data pipeline that combines multiple data sources (APIs, databases, web pages)
- Designing a custom dataset schema for business intelligence or research
- Enriching existing data with information from the web
- Automating recurring data extraction or transformation tasks
- Processing unstructured content (reports, pitch decks, contracts) into queryable tables
- Creating relationships between entities (companies, people, investments)

Do not use Structify for: simple CSV transformations, basic data validation, or tasks that don't require web extraction or AI-powered enrichment.

## Quick Reference

### Core Concepts

| Concept | Definition | Use Case |
|---------|-----------|----------|
| **Dataset** | Container for structured data with schema, tables, and relationships | Organize all data for a project (e.g., "startup_ecosystem") |
| **Table** | Entity type with properties (e.g., "company", "founder") | Define what kind of entities you're tracking |
| **Property** | Attribute of an entity (e.g., "name", "revenue") | Store specific data points with types (String, Integer, Money, Date, URL, Boolean, Enum) |
| **Relationship** | Typed connection between entities (e.g., "founded_by", "invested_in") | Model connections with optional properties (e.g., investment amount, date) |
| **Job** | Asynchronous task tracking extraction or enrichment | Monitor long-running operations |

### Python SDK Quick Start

```python
from structify import Structify

# Initialize client (uses STRUCTIFY_API_TOKEN env var)
client = Structify()

# Create dataset
client.datasets.create(name="my_data", tables=tables, relationships=relationships)

# Add entities
client.entities.add(dataset="my_data", kg=KnowledgeGraphParam(...))

# Enrich with web data
client.structure.enhance_property(entity_id=entity_id, property_name="description")
client.structure.enhance_relationship(entity_id=entity_id, relationship_name="founded_by")

# Query
results = client.entities.search(dataset_name="my_data", query="AI companies")

# Export
csv_data = client.datasets.export_to_csv(name="my_data")
```

### Five AI Extraction Functions

| Function | Purpose | When to Use |
|----------|---------|------------|
| **enhance_columns** | Auto-find web sources and add missing data to existing records | Default choice; enriching data without knowing source URLs |
| **enhance_relationships** | Find related entities (founders, investors, employees) | Building networks or finding connections |
| **scrape_columns** | Extract one value per webpage from known URLs | You have specific URLs and need one piece of data per page |
| **scrape_relationships** | Extract lists from known URLs | You have URLs with lists (job openings, products, team members) |
| **structure_pdfs** | Convert PDF documents into data tables | Processing reports, invoices, contracts |

### Property Types

```python
Property(name="field", prop_type="String")      # Default
Property(name="count", prop_type="Integer")
Property(name="revenue", prop_type="Money")
Property(name="date", prop_type="Date")
Property(name="url", prop_type="URL")
Property(name="active", prop_type="Boolean")
Property(name="rating", prop_type="Float")
Property(name="logo", prop_type="Image")
Property(name="status", prop_type=Enum(Enum=["Active", "Closed", "Acquired"]))
```

## Decision Guidance

### When to Use enhance_columns vs scrape_relationships

| Scenario | Use enhance_columns | Use scrape_relationships |
|----------|-------------------|------------------------|
| You know the exact URLs | No | Yes |
| You need to find sources automatically | Yes | No |
| Extracting one value per row | Yes | Maybe |
| Extracting lists from pages | No | Yes |
| General data enrichment | Yes | No |
| Complex multi-step extraction | Combine both | Combine both |

**Recommended workflow:** Start with `enhance_columns` to find relevant pages, then use `scrape_relationships` to extract detailed lists from those pages.

### When to Use Web Scraping vs Document Upload

| Approach | Best For | Limitations |
|----------|----------|------------|
| Web scraping (enhance/scrape functions) | Public websites, APIs, continuously updated data | Requires internet-accessible sources |
| Document upload (structure_pdfs) | Internal reports, pitch decks, contracts, PDFs | One-time processing, no ongoing updates |
| Connectors | Databases, CRMs, internal systems | Requires authentication setup |

## Workflow

### Building a Data Pipeline (Web + API)

1. **Define your schema** — Create tables for each entity type (company, person, investor) with descriptive properties and relationships. Use strong typing (Money, Date, URL, Enum).

2. **Add seed data** — Start with initial entities (company names, URLs, or seed records) using `client.entities.add()`.

3. **Enrich with web data** — Use `enhance_columns` to auto-find sources and fill missing properties. Use `enhance_relationships` to find connected entities.

4. **Validate results** — Check the chat message for issues. Use "Fix with AI" button if errors appear. Review sample data.

5. **Add transformations** — Ask the AI agent to add new blocks for aggregation, filtering, or visualization.

6. **Schedule or export** — Use the Schedule button for recurring runs, or export to CSV via `client.datasets.export_to_csv()`.

### Processing Documents (PDF Extraction)

1. **Upload the document** — Use `client.documents.upload(file_path="file.pdf", dataset_name="my_data")`.

2. **Define extraction prompt** — Clearly describe what to extract and what it means (e.g., "Extract company name, founders, funding amounts, and dates").

3. **Structure the document** — Call `client.documents.structure(document_id=doc.id, dataset_name="my_data", extraction_prompt="...")`.

4. **Check job status** — Poll `client.jobs.get(job_id=job.id)` until status is "completed".

5. **Verify extracted data** — Query the dataset to confirm entities and relationships were created correctly.

### Using the Web UI (No Code)

1. **Create a chat** — Click "New Chat" in the Structify app.

2. **Attach data** — Use "Attach file" or "Connect input" to add CSVs, PDFs, or connect to databases.

3. **Write a prompt** — Describe your business logic in natural language (e.g., "Extract company names and founders from this list, then find their funding information").

4. **Let the AI build the pipeline** — The agent generates code blocks for data extraction and transformation.

5. **Refine with follow-ups** — Ask to add visualizations, change logic, or add new blocks. Use "Fix with AI" for errors.

6. **Schedule or share** — Use Schedule button for recurring runs, Share button to collaborate.

## Common Gotchas

- **Vague property descriptions fail** — Always write clear, specific descriptions for tables and properties. The AI uses these to understand what to extract. "Revenue" is bad; "Annual revenue in USD for the most recent fiscal year" is good.

- **Forgetting to set property types** — Use strong typing (Money, Date, URL, Integer) instead of leaving everything as String. This improves extraction accuracy and enables validation.

- **Over-constraining enums** — Don't create enums with only exact values. Always include "Other" for edge cases.

- **Circular relationship dependencies** — Avoid self-referential relationships that create loops. Test with sample data first.

- **Missing connectors** — If you're pulling from internal databases or APIs, you must set up connectors first in the Connectors view or in the chat.

- **No data but no error** — Check the chat message. The AI agent logs programmatic issues there. If unclear, tell the agent "I'm missing data in node X" and it will diagnose.

- **Editing a block re-runs everything below it** — When you edit a specific block, all blocks downstream re-execute. Usually you want to add new blocks instead of editing existing ones.

- **Rate limits** — Free tier: 60 requests/min, 2 concurrent jobs. Pro: 600 requests/min, 10 concurrent jobs. Implement exponential backoff for rate limit errors (429).

- **API key exposure** — Never commit API keys to version control. Always use environment variables (`STRUCTIFY_API_TOKEN`).

- **Stale client library** — Keep the SDK updated: `pip install structifyai --upgrade`.

## Verification Checklist

Before submitting work with Structify:

- [ ] Schema has descriptive text for all tables, properties, and relationships
- [ ] All property types are set correctly (not everything is String)
- [ ] Enums include an "Other" option for edge cases
- [ ] Sample data has been tested with the schema (no extraction failures)
- [ ] Web sources are accessible (not behind login or paywalls)
- [ ] Connectors are configured if using internal data sources
- [ ] Pipeline runs without errors (check Terminal tab in Dev Tools)
- [ ] Extracted data matches expected format and completeness
- [ ] API key is stored in environment variable, not hardcoded
- [ ] Rate limits are respected (implement backoff for 429 errors)
- [ ] Job status is checked before assuming completion
- [ ] Results are exported or scheduled as needed

## Resources

**Comprehensive navigation:** https://docs.structify.ai/llms.txt

**Critical documentation pages:**
- [API Quickstart](https://docs.structify.ai/api-reference/quickstart) — Create your first dataset with Python SDK
- [Schema Design Guide](https://docs.structify.ai/schema-cookbook/schema-guidance) — Best practices for designing schemas
- [Structify Calls Guide](https://docs.structify.ai/structify-calls) — How to use the five extraction functions effectively

---

> For additional documentation and navigation, see: https://docs.structify.ai/llms.txt