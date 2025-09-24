# Better DDD Architecture

Repo: TBD

## Current layout

- `server` is the main workspace package to run the axum server
- `cli`: to be added, will be a clap interface
- both of the above need to interface with `domain` level interfaces.
- consider handling jobs via a Tokio job scheduler.

### Services

The are the primary entry point for now. Axum handlers will access services via the State extractor. This wraps access to the DB.

- services call interfaces (currently inside `application::gateway::repository`) and these in turn call to `infrastructure/postgres_database`

### Pain points

- better organisation
  - should this more hexagonal than DDD?
  - consider having a better definition of ports/adapters
- should I continue to keep all the heavy logic in `domain`?

#### Testing

- Better way to address
  - unit testing
  - tests at the service level, these may also need access to `domain` for value types.
  - E2E/integration tests

```shell
.
├── CHANGELOG.md
├── Cargo.lock
├── Cargo.toml
├── Justfile
├── README.md
├── application
│   ├── Cargo.toml
│   ├── gateway
│   │   ├── mod.rs
│   │   └── repository
│   ├── lib.rs
│   └── services
│       ├── mod.rs
│       ├── tasks.rs
│       ├── user_auth.rs
│       └── users.rs
├── domain
│   ├── Cargo.toml
│   ├── common
│   │   ├── formatting.rs
│   │   ├── misc.rs
│   │   └── mod.rs
│   ├── entity
│   │   ├── auth
│   │   ├── auth.rs
│   │   ├── mod.rs
│   │   ├── note.rs
│   │   ├── task.rs
│   │   └── user.rs
│   ├── lib.rs
│   └── value_type
│       ├── mod.rs
│       └── uid.rs
├── infrastructure
│   └── postgres_database
│       ├── Cargo.toml
│       ├── lib.rs
│       ├── tasks.rs
│       ├── user_auth.rs
│       └── users.rs
├── migrations
│   ├── 20241210180612_setup.down.sql
│   ├── 20241210180612_setup.up.sql
│   ├── 20250116153608_create_user_table.down.sql
│   ├── 20250116153608_create_user_table.up.sql
│   ├── 20250724122402_create_notes.down.sql
│   ├── 20250724122402_create_notes.up.sql
│   ├── 20250810112603_create_tasks.down.sql
│   ├── 20250810112603_create_tasks.up.sql
│   ├── 20250824100828_create_verification_code.down.sql
│   └── 20250824100828_create_verification_code.up.sql
├── server
│   ├── Cargo.toml
│   └── src
│       ├── custom_error.rs
│       ├── main.rs
│       ├── misc.rs
│       ├── models
│       ├── services
│       └── tests
├── test_env.sh
├── testing_tools
│   ├── Cargo.toml
│   ├── error.rs
│   ├── lib.rs
│   └── utils.rs

58 directories, 82 files

```
