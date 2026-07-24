---
name: Create a data asset and share it
description: Create a Code Ocean data asset from an S3 bucket or a computation, confirm it, and grant access to users, groups, or everyone.
api: openapi/code-ocean-openapi.yml
operations: [createDataAsset, getDataAsset, updateDataAssetPermissions]
---

# Create a data asset and share it

Use this to register a dataset (or captured result) as a Code Ocean Data Asset and control who can access it.

## Auth
HTTP Basic with your `cop_` access token as the username. Requires the Data Asset Write scope.

## Steps
1. **Create** — `POST /data_assets` (`createDataAsset`) with `name`, `mount`, `tags`, and `source` (an S3 bucket or a computation). The response is a Data Asset object; keep its `id`.
2. **Confirm ready** — `GET /data_assets/{data_asset_id}` (`getDataAsset`) and check `state` until the asset is ready (the ingest is asynchronous).
3. **Share** — `POST /data_assets/{data_asset_id}/permissions` (`updateDataAssetPermissions`) with `users`, `groups`, and `everyone`; set `share_assets: true` to cascade sharing.

## Rules
- `mount` is where the asset appears inside a capsule at run time — keep it stable across runs.
- A `403` on the permissions call means your token lacks Data Asset Write or you don't own the asset.
- See `conventions/code-ocean-conventions.yml` for the auth and error-envelope contract.
