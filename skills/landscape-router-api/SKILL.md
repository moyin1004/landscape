---
name: landscape-router-api
description: Operate Landscape Router via its REST OpenAPI spec in `landscape-types/openapi.json`, including logging in at `POST /api/auth/login` to obtain a bearer token before calling protected endpoints. Use when you need to plan or execute API calls for router management (interfaces, routes, DHCP/DNS, firewall, certificates, devices, system config/info, logs, metrics, and related services).
---

# Landscape Router API

## Overview
Use this skill to call Landscape Router REST endpoints with correct authentication and response handling, guided by the OpenAPI spec in `landscape-types/openapi.json`.

## Workflow
1. Determine the base URL (host, scheme, port). The OpenAPI spec does not define servers, so ask the user if it is not provided.
2. Authenticate:
   - `POST /api/auth/login` with JSON body matching `LoginInfo` (`username`, `password`).
   - Parse the token from `data.token` in the response envelope.
   - Treat `error_id` presence, HTTP 401, or missing token as a failure and stop.
3. Call protected endpoints:
   - Add header `Authorization: Bearer <token>` on all non-login requests.
   - Use `Content-Type: application/json` when sending JSON bodies.
   - Use the OpenAPI spec to find the exact path, method, parameters, and schemas.
4. Handle responses:
   - Most responses are wrapped as `LandscapeApiResp_*` with `data`, `error_id`, `message`, and `args`.
   - Treat non-empty `error_id`, missing `data`, or HTTP >= 400 as errors and surface `message`.

## Endpoint Discovery
- Spec location: `landscape-types/openapi.json`.
- Quick path search: `grep -n "\"/api/" landscape-types/openapi.json`.
- Tags are listed in the spec near the end; use them to navigate areas.
- For schema details, search for the referenced schema name under `components.schemas`.

See `references/openapi.md` for a compact reference of auth and response envelope details.

## Examples
Login and capture token:
```bash
BASE_URL="http://router.local"
TOKEN=$(curl -s -X POST "$BASE_URL/api/auth/login" \
  -H 'Content-Type: application/json' \
  -d '{"username":"admin","password":"<password>"}' \
  | python3 -c 'import sys, json; print(json.load(sys.stdin).get("data", {}).get("token", ""))')
```

Call a protected endpoint (list enrolled devices):
```bash
curl -s "$BASE_URL/api/v1/devices/all" \
  -H "Authorization: Bearer $TOKEN"
```
