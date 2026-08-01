---
name: Export an Insights report
description: Run an asynchronous Sana Insights report from a SQL query and download the result file.
api: openapi/sana-openapi.yml
operations: [createAccessToken, createInsightsReport, getInsightsReportJob]
---

# Export an Insights report (Sana)

Sana's Insights API turns a widget's SQL query into a downloadable export via an asynchronous job.

## Auth
1. `createAccessToken` — `POST /api/token` (client credentials, `scope=read,write`). Use `Authorization: Bearer <accessToken>`.

## Steps
1. `createInsightsReport` — `POST /api/v1/reports/query` with `{ "query": "<SQL from your Insights widget>", "format": "csv" | "xlsx" }`. Response returns a `jobId` and `status` (`pending`).
2. `getInsightsReportJob` — `GET /api/v1/reports/jobs/{jobId}`. Poll until `status` is `successful`, then download from `link.url`.

## Rules
- The job is asynchronous — poll with backoff rather than blocking.
- `format` must be `csv` or `xlsx`.
- The `query` comes from an existing Insights widget configuration; do not hand-craft arbitrary SQL beyond what the widget exposes.
