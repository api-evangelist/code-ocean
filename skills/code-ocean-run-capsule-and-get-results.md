---
name: Run a capsule and retrieve its results
description: Execute a Code Ocean capsule or pipeline, poll the computation to completion, list its result files, and fetch signed download URLs.
api: openapi/code-ocean-openapi.yml
operations: [runCapsule, getComputation, listComputationResults, getResultFileURLs]
---

# Run a capsule and retrieve its results

Use this to run a reproducible Code Ocean capsule (or pipeline) and collect the output files.

## Auth
HTTP Basic. Send your access token (prefix `cop_`) as the basic-auth username with an empty password: `-u "cop_xxxxxxxxxx:"`. Requires the Capsule Read scope; running requires run permission on the capsule.

## Steps
1. **Start the run** — `POST /computations` (`runCapsule`) with a body containing `capsule_id` (or `pipeline_id`), plus optional `data_assets`, `parameters`/`named_parameters`. The response is a Computation object; keep its `id`.
2. **Poll to completion** — `GET /computations/{computation_id}` (`getComputation`) until `state` is `completed` (or `failed`). Back off between polls; the Computation API returns `429` under load, so wait before retrying.
3. **List result files** — `POST /computations/{computation_id}/results` (`listComputationResults`) with an optional `{ "path": "" }` body (empty path = `/results` root). Read the `items[]` (name, path, size, type).
4. **Get download URLs** — `GET /computations/{computation_id}/results/urls?path={file_path}` (`getResultFileURLs`) to obtain short-lived `download_url` and `view_url` for each file you want.

## Rules
- Check `has_results` on the computation before listing files.
- Errors are plain JSON `{ "message": "..." }`; `401` = bad token, `403` = missing scope, `423` = workspace locked. See `errors/code-ocean-problem-types.yml`.
