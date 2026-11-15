# Integration and publication notes

## Status

**Source excerpt for code review, not a self-contained application.** The 22 supplied code files preserve their original relative imports. They cannot be built or tested without files from the parent application. Do not interpret the reported 17/17 and 28/28 counts as independent validation of this archive.

## Dependencies intentionally not bundled

- Angular bootstrap, workspace/TypeScript configuration, `package.json`, environment and translation JSON files.
- `AuthService`, `MessageWrapperService`, component test providers and shared authentication schemas.
- Shared `common.schema` (used by the reporting Zod schema).
- Express app/bootstrap, error handler, request-context, `CustomError`, database configuration, Sequelize models/associations and test setup.
- Server test factories and authentication/password helpers referenced by the integration suite.
- Other production-specific support modules imported transitively.

The `shared/dist/...` paths used by the server are build outputs of the original shared TypeScript package; the corresponding source files are included under `shared/`.

## One narrowly scoped compatibility adjustment

`client/src/app/services/reports.service.spec.ts`: the School Coordination request fixture was updated from `userId: 42` to `userId: null`, matching the updated server authorization contract and component logic. No other supplied application code was modified in this archive. The service test suite has **not** been re-run after this change.

## Before making the repository public

1. Confirm that the foundation/project owner permits public publication of the source code, even in excerpt form.
2. Review business terminology, code comments and schemas for any information that should remain confidential.
3. If reviewers require executable tests, provide a separate sanitized runnable harness rather than claiming that this excerpt is standalone.
4. Check the repository history as well as the working files for secrets before pushing.
5. Confirm any framework versions and installation instructions against the original project's package-lock/package.json before adding them.

## Privacy review performed here

A targeted scan found no embedded API credentials or private keys in the 22 supplied files. `Password123` appears as a synthetic integration-test password, and `report.volunteer@example.com` is an example contact address. This scan is not a substitute for source-ownership approval or a comprehensive security audit.
