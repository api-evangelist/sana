---
name: Provision users and enroll them in a program
description: Authenticate to Sana, create or look up users, and enroll them into a learning program using the Sana REST API.
api: openapi/sana-openapi.yml
operations: [createAccessToken, listUsers, createUser, listPrograms, enrollProgramMembers]
---

# Provision users and enroll them in a program (Sana)

Use the Sana REST API (served per tenant at `https://<domain>.sana.ai`) to onboard learners and place them into a program.

## Auth
1. `createAccessToken` — `POST /api/token` with `grant_type=client_credentials`, your `client_id`, `client_secret`, and `scope=read,write`. The response returns `accessToken` (bearer, `expiresIn` 3600s).
2. Send `Authorization: Bearer <accessToken>` on every subsequent request. GET needs the `read` scope; writes need `write`.

## Steps
1. `listUsers` — `GET /api/v0/users` (cursor pagination: `limit` up to 1000, follow `next`) to check whether the learner already exists by email.
2. `createUser` — `POST /api/v0/users` with `email` (required) and optional `firstName`, `lastName`, `role` (`learner`|`group-admin`|`admin`), `language`. Capture the returned user id.
3. `listPrograms` — `GET /api/v0/programs` to find the target `programId`.
4. `enrollProgramMembers` — `POST /api/v0/programs/{programId}/members` with the user ids to enroll.

## Rules
- No idempotency-key is supported; guard against duplicate creates by looking up the user first (step 1).
- Reporting on progress is asynchronous — see the `Create an Insights report` skill.
