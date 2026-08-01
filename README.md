# accessiBe

accessiBe is a web accessibility technology company whose products help organizations make websites and web applications usable by people with disabilities and compliant with WCAG, the ADA, Section 508, AODA and the European Accessibility Act. Its portfolio spans accessWidget (an AI-driven remediation layer and end-user accessibility interface installed via an embed script), accessScan (a free single-URL compliance checker), accessFlow (a developer-first accessibility testing and monitoring platform with an SDK for Playwright, Cypress and Selenium plus CI/CD integrations), and accessServices (expert audits, user testing by people with disabilities, file and PDF remediation, and VPAT authoring).

- Website: https://accessibe.com
- Support: https://accessibe.com/support
- Status: https://status.accessibe.com
- Trust center: https://trust.accessibe.com

## APIs

| API | Docs | Base URL | Auth |
|---|---|---|---|
| Partners API License 1.0.1 | https://dashboard.accessibe.com/api/v1/partners/docs | https://dashboard.accessibe.com/api/v1/partners | `x-api-key` header (partner account) |
| accessFlow MCP Server (beta) | https://www.npmjs.com/package/accessflow-mcp-server | https://flow-mcp.accessibe.com/mcp | Bearer AI MCP token + `X-Domain` |

The Partners API covers accessWidget license lifecycle management: create, read, list,
filter and update licenses, plus asynchronous batch create/update of up to 150 licenses
with a batch status endpoint. It supports cursor pagination (`nextToken` + `limit`),
`expand[]=settings` field expansion, and an `Idempotency-Key` header on the batch
endpoints.

The accessFlow MCP server exposes three tools — `getMostUrgentIssues`,
`getIssueRemediation` and `resolveIssue` — over stdio and hosted HTTP transports. It has
no REST equivalent; see `mcp/accessibe-tool-crosswalk.yml`.

## Artifacts

`openapi/` `overlays/` `mcp/` `skills/` `packages/` `llms/` `authentication/`
`conventions/` `errors/` `lifecycle/` `changelog/` `conformance/` `data-model/`
`components/` `security/` `agentic-access/` `well-known/`

No A2A agent card, no AsyncAPI or first-party webhook surface, no `/.well-known/`
discovery documents, no public vulnerability-disclosure program and no OAuth surface
were found. Those absences are recorded in the relevant artifacts rather than
manufactured.
