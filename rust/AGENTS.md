# Agent Development Guidelines

## Instruction Precedence

- When development guidelines conflict, follow this order of precedence:
  **Project `AGENTS.md` → project rules → global `AGENTS.md` → global rules**.

## Use Cargo

- When modifying the root `Cargo.toml` or any workspace member's `Cargo.toml`, use Cargo commands whenever Cargo supports the change. Do not edit these files directly or rewrite them with scripts in those cases.
- Create new packages with `cargo new` or, where appropriate, `cargo init`. For example:

  ```bash
  cargo new apps/<name> --bin --vcs none
  cargo new crates/<name> --lib --vcs none
  ```

- Use the appropriate Cargo commands to manage dependencies. When adding or migrating third-party dependencies, follow the approval requirements in the next section:

  ```bash
  cargo add -p <package> <dependency>
  cargo rm -p <package> <dependency>
  ```

- Edit manifest fields directly only when Cargo does not support the required change, such as configuring `[profile.dev.package."*"]`. Before editing, explain which change Cargo does not support and why. Limit the edit to the necessary fields.
- A failed command, an unavailable network connection, or a missing subcommand does not mean Cargo lacks support for the change. None of these conditions permits bypassing this rule.
- Preserve dependency versions, features, and Git revisions unrelated to the task. Do not perform unrelated dependency updates.
- Let Cargo generate and maintain `Cargo.lock`; never edit it by hand. Review all manifest and lockfile changes produced by Cargo to ensure they stay within the authorized scope.

## Dependency Changes

- Obtain user approval before adding or replacing a third-party dependency, or changing its version requirement, features, registry, or Git source. An explicit request naming the dependency and the change counts as approval.
- Keep approval scoped to the requested packages and changes. Do not include unrelated dependency or lockfile updates.
- Remove a dependency only when requested or when the authorized change makes it unused. Ask before removing it for any other reason.
- Local workspace dependencies do not require third-party dependency approval, but changes must remain within the requested scope.

## Development Workflow and Quality Checks

Follow this sequence for both feature development and bug fixes. Run all commands from the workspace root:

1. Implement the requested functionality and any necessary changes to calling code. At each development milestone, ensure formatting, compilation, and static analysis checks pass. Resolve issues reported by these checks before proceeding:

   ```bash
   cargo fmt --all -- --check
   cargo check --workspace --all-targets
   cargo clippy --workspace --all-targets -- -D warnings
   ```

2. Once the implementation is complete and all formatting, compilation, and static analysis checks pass, write the necessary unit and integration tests.
3. After writing the tests, rerun the checks above, then run:

   ```bash
   cargo test --workspace
   ```

- Limit formatting fixes to code involved in the task. Workspace-wide checks do not authorize reformatting unrelated files.
- If interface changes prevent existing tests from compiling, make the necessary compatibility changes first. Do not expand test coverage ahead of the testing stage or remove valid assertions to avoid failures.
- If platform constraints, system permissions, or external conditions prevent a check from completing, report the command, the blocker, and what remains unverified. Do not claim the check passed.

## Test Organization

- Keep unit tests inline in a `#[cfg(test)] mod tests` module within the source file being tested. Do not split them into separate test module files.
- Place integration tests in the corresponding crate's `tests/` directory as needed.
