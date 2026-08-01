---
name: Scrape the web into a structured dataset
description: Use Structify to turn websites/PDFs into a structured dataset, then export the results.
api: openapi/structify-openapi-original.json
operations: [dataset_create, scrape, get_job, entity_search, dataset_export_csv]
---

# Scrape the web into a structured Structify dataset

Structify turns unstructured sources (websites, PDFs) into structured entities in
a dataset. Scraping is asynchronous: you start a job, poll it, then read/export.

## Auth
Send `api_key: <YOUR_API_KEY>` header (or a Bearer session token). The SDK reads
`STRUCTIFY_API_TOKEN`. See `authentication/structify-authentication.yml`.

## Steps
1. **Create a dataset** with the schema you want — call `dataset_create` with a
   name and description. Keep the dataset id/name.
2. **Start a scrape** — call `scrape` with the target and the dataset. This
   returns a job (async).
3. **Poll the job** — call `get_job` with the `job_id` until `status` is
   `completed`. Use exponential backoff; optionally subscribe to job events
   (`/jobs/{job_id}/events`) or WebSocket `job.completed` instead of polling.
4. **Read results** — call `entity_search` against the dataset to inspect the
   extracted entities.
5. **Export** — call `dataset_export_csv` to download the structured dataset.

## Rules
- Pagination is `limit`/`offset`. Rate limits: Free 60/min (2 concurrent jobs),
  Pro 600/min (10). Back off on 429/rate-limit errors.
- No idempotency-key contract — do not blindly retry non-GET calls; re-poll the
  job instead. See `conventions/structify-conventions.yml`.
- Errors are `application/json`; handle 400/401/403/404/500 per
  `errors/structify-error-codes.yml`.
