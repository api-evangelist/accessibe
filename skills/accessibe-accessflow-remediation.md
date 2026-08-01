---
name: Triage and remediate accessibility issues with the accessFlow MCP server
description: Use the accessFlow MCP tools to pull the most urgent WCAG issues for a domain, fetch code-level remediation guidance for a specific issue, apply the fix, and mark it resolved.
api: mcp/accessibe-mcp.yml
generated: '2026-07-31'
method: generated
source: mcp/accessibe-mcp.yml
operations:
  - getMostUrgentIssues
  - getIssueRemediation
  - resolveIssue
---

# Triage and remediate accessibility issues with the accessFlow MCP server

Server: `https://flow-mcp.accessibe.com/mcp` (hosted HTTP) or
`npx -y accessflow-mcp-server` (stdio). Status: beta.

## Before you start

- Generate an **AI MCP token** in accessFlow: *Profile → Token management → Generate
  token → AI MCP token*. It is displayed once; store it in a secret manager.
- Hosted transport requires two headers: `Authorization: Bearer <token>` and
  `X-Domain: <the domain being analyzed>`.
- Stdio transport requires two environment variables: `API_KEY` and `DOMAIN`.
- Every tool accepts `auditSource`. `PLATFORM` (the default) reads the latest
  accessFlow platform audit of the live site. `SDK` reads a CI/CD audit produced by
  the accessFlow SDK and **requires `commitId`**; narrow further with `repo` and
  `branch` when a token spans several repositories.

## Steps

1. **Pull the triage queue.** Call `getMostUrgentIssues`. Results are ordered by
   severity (`Extreme` → `High` → `Medium` → `Low`), then by how many locations across
   the site are affected, then by occurrences on individual pages. Each row carries
   severity, WCAG level, occurrence counts, and a unique identifier of the form
   `Decorative-Content-6d277a13ba`.

2. **Pick one issue and get the fix.** Call `getIssueRemediation` with
   `issueDisplayName` set to that identifier. You get back the problem summary and its
   WCAG references, the current problematic markup, suggested code fixes with examples,
   step-by-step instructions, and tutorial links.

3. **Apply the fix in the codebase**, not in the widget. This flow is for fixing source
   code — accessWidget remediation is a separate, runtime product.

4. **Close the loop.** Call `resolveIssue` with the same `issueDisplayName`. If the next
   audit still detects the issue it is **reopened automatically**, so resolving is a
   claim that will be re-verified, not a suppression.

5. **Repeat from step 1** until the Extreme and High tiers are clear.

## Rules

- Always carry the same `auditSource` (and `commitId` when `SDK`) across all three
  calls in one triage pass. Mixing a `PLATFORM` issue id into an `SDK` remediation call
  will not resolve to the same finding.
- Do not resolve an issue you have not actually fixed — the next audit reopens it and
  you lose the signal.
- The live `tools/list` endpoint is auth-gated (an anonymous POST returns `401`), so
  discover the tool set through your MCP client after authenticating rather than by
  probing.
- This surface has **no** REST equivalent: the Partners API manages licenses, not
  accessibility findings. See `mcp/accessibe-tool-crosswalk.yml`.
