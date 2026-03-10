# Landscape Router OpenAPI Reference (Quick)

## Spec Location
- `/root/workspace/landscape/landscape-types/openapi.json`

## Authentication
- Login: `POST /api/auth/login`
- Body schema: `LoginInfo` (`username`, `password`)
- Response envelope: `LandscapeApiResp_LoginResult`
- Token location: `data.token`
- Use on subsequent requests: `Authorization: Bearer <token>`

## Response Envelope
Most responses use `LandscapeApiResp_*`:
- `data`: main payload
- `error_id`: non-empty indicates a failure
- `message`: human-readable error or status message
- `args`: optional request args echo

## Finding Endpoints
- Search paths: `grep -n "\"/api/" landscape-types/openapi.json`
- For schema details: search the schema name under `components.schemas`
