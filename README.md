# Azure Test Plan to Playwright

Reusable GitHub Copilot skill for assisted generation of Playwright tests from
Azure DevOps Test Plans. It retrieves and normalizes one case, pauses for approval,
inspects application code, generates automation, runs the specific test, and
analyzes failures before human review. It never automatically commits, pushes,
opens pull requests, or updates Azure DevOps.

## Use

The skill lives in
`.github\skills\azure-testplan-to-playwright\SKILL.md`, following
[GitHub Copilot's project skill convention](https://docs.github.com/en/copilot/concepts/agents/about-agent-skills).
Open this repository in a skill-capable client, or copy the skill folder into
`.github\skills` in the application repository. The application must be available
in an authorized workspace; this repository does not contain its source code.

Invoke the skill by name when supported, or ask Copilot to use
`azure-testplan-to-playwright` to automate one ADO case.

Persistent configuration contains only `azureDevOps.organization` and
`azureDevOps.project`. Provide them inline or identify a JSON file based on
`.github\skills\azure-testplan-to-playwright\config.example.json`, which uses
`aiturralde` and `PartsUnlimited`.

Specify the Test Case to implement each time you invoke the skill. Case, plan,
and suite IDs are not configuration settings. A case ID can be retrieved directly;
plan/suite context is discovered only when needed. If the case selection is
missing or ambiguous, the skill asks you to choose instead of selecting one.

Run in the authorized application workspace. The skill discovers Playwright
settings, authentication, environment-variable names, and test-data conventions
from the repository, asking for missing context at runtime only when needed.
These are not persistent skill settings. Keep secret values out of chat and files.

Example request:

> Use azure-testplan-to-playwright with the example configuration to implement
> Test Case 1420. Show sanitized source fields and the normalized interpretation, and stop
> for my approval. Do not generate code yet.

After approval, the skill continues with feasibility, repository-backed locators,
test generation, and execution. Missing application context blocks those phases;
it is not replaced with guessed selectors or behavior.

## Azure DevOps MCP

`.vscode\mcp.json` configures Microsoft's remote MCP for `aiturralde`, with
`testplan` and `wit` toolsets and the read-only header. `PartsUnlimited` is passed
as project context to tool calls. Authenticate through your MCP client using an
account with access to the project; no token is stored in this repository.

This VS Code configuration does not automatically expose MCP tools to a different
Copilot client/session. Configure the server in that client as needed.
Remote MCP requires a Microsoft Entra-backed organization and a compatible client;
see [Microsoft's setup documentation](https://learn.microsoft.com/en-us/azure/devops/mcp-server/remote-mcp-server?view=azure-devops).
Do not introduce a custom REST integration before validating MCP capabilities.

## Evidence and current limits

User-supplied output from a separate VS Code session reported:

- Plan 1421 and 12 suites, with no continuation token.
- Case 1420 from suite 1423, including five action/expected-result pairs through
  `wit_work_item` retrieval.
- An absent description, empty `<parameters />`, and no explicit Shared Step
  references in this case.

That output supports using official MCP for this first retrieval; it does not
verify Shared Step expansion or parameterized data retrieval. The skill checks
those capabilities when relevant instead of assuming support.

Case 1420 concerns login, category selection, product details, adding to cart,
and estimated shipping dates for a postal code. No case automation has been
generated or executed here. The application checkout, reliable locator evidence,
approved test data, and explicit case approval are still required.
