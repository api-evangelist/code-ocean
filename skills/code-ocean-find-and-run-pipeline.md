---
name: Find a pipeline, attach data, and run it
description: Search Code Ocean pipelines, attach the data assets a pipeline needs, run it, and track the computation.
api: openapi/code-ocean-openapi.yml
operations: [searchPipelines, attachDataAssets, runCapsule, getComputation]
---

# Find a pipeline, attach data, and run it

Use this to discover a pipeline, wire in its input data, and launch a run.

## Auth
HTTP Basic with your `cop_` access token. Needs Capsule Read/Write and Data Asset Read/Write scopes.

## Steps
1. **Search** — `POST /pipelines/search` (`searchPipelines`) with a body of `query`, `limit`, `sort_field`, `sort_order`, and optional `next_token` for cursor pagination. Read `results[]` and `has_more`; keep the target pipeline's `id`.
2. **Attach data** — `POST /capsules/{capsule_id}/data_assets` (`attachDataAssets`) with a list of `{ "id": "<data_asset_id>", "mount": "<path>" }`. Check each result's `ready` flag.
3. **Run** — `POST /computations` (`runCapsule`) with `pipeline_id`, plus `parameters`/`named_parameters` and, for pipelines, optional `processes` and `nextflow_profile`. Keep the returned computation `id`.
4. **Track** — `GET /computations/{computation_id}` (`getComputation`) until `state` is `completed`. Back off on `429`.

## Rules
- Search is cursor-paginated: pass the previous response's `next_token` to page forward while `has_more` is true.
- Never invent operationIds — every step above exists in `openapi/code-ocean-openapi.yml`.
