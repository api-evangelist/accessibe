# accessiBe

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

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
