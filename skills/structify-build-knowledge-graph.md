---
name: Build a knowledge graph of entities and relationships
description: Create a Structify dataset, add entities (nodes), and connect them with typed relationships.
api: openapi/structify-openapi-original.json
operations: [dataset_create, entity_add, entity_add_batch, entity_add_relationship, entity_search]
---

# Build a Structify knowledge graph

A Structify dataset is a typed knowledge graph: entities (nodes) in tables,
connected by directional, typed relationships.

## Auth
Send `api_key: <YOUR_API_KEY>` header (or Bearer session token / `STRUCTIFY_API_TOKEN`).
See `authentication/structify-authentication.yml`.

## Steps
1. **Create the dataset** — call `dataset_create` with a name and description.
2. **Add entities** — call `entity_add` (single) or `entity_add_batch` (many)
   with the target dataset, table name, and the entity properties.
3. **Connect entities** — call `entity_add_relationship` with the source entity
   id, target entity id, a relationship name (e.g. "Owns"), and optional
   properties. Relationships are directional (source → target).
4. **Query** — call `entity_search` to retrieve entities and traverse the graph.

## Rules
- Use `entity_add_batch` for bulk loads to stay under rate limits (Free 60/min,
  Pro 600/min).
- Pagination is `limit`/`offset`; results are `application/json`.
- On 409 Conflict (optimistic concurrency), re-fetch and retry. Full error map:
  `errors/structify-error-codes.yml`. Conventions:
  `conventions/structify-conventions.yml`; data model:
  `data-model/structify-data-model.yml`.
