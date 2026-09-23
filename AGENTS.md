# Financial Agent Repository Guidance

## Start Here

- Read `docs/README.md` before project work when it exists.
- Load only the authoritative documents relevant to the current task.
- Read the active milestone plan before implementation.
- Report conflicts between code, plans, ADRs, and architecture instead of silently choosing one.
- Preserve unrelated user changes.

## Documentation

- Update the single authoritative document and link to it instead of duplicating content.
- Create a new ADR for durable cross-module decisions; never rewrite accepted ADR history.
- Keep product intent, architecture, plans, runbooks, and reference facts in their owning directories.
- Keep maintained document metadata current.
- Keep implementation mechanics in plans and runbooks.
- Exclude temporary mockups, generated output, browser state, and debug files.

## Engineering Boundaries

- Preserve the modular monolith and explicit business-module boundaries.
- Keep domain code independent of Tushare, Feishu, n8n, Spring AI, model SDKs, and cloud vendors.
- Access external systems through ports and adapters.
- Keep controllers and Agent tools free of core financial calculations.
- Version database changes with migrations.
- Test deterministic financial calculations.
- Use contract tests or stable fixtures for external services.
- Cover critical user flows with integration tests.

## Security and Git

- Never read, print, copy, upload, or commit credentials, private keys, passphrases, tokens, `.env` files, production data, or personal data.
- Keep commits focused and verify affected files before committing.
- Avoid unrelated formatting and refactors.
- Use the `codex/` prefix for newly created branches unless the user specifies another name.
- Do not push, force-push, rewrite published history, create a pull request, or change remote state without explicit user authorization.
