# Better DDD Architecture

Repo: TBD

# Proposed directory tree

- Need to improve this further

```shell
.
├── Cargo.toml                # workspace root
├── Justfile                  # tasks
├── README.md
│
├── domain/                   # Pure domain logic
│   ├── accounting/           # Accounts, journal entries, postings
│   ├── users/                # User aggregate, ownership, UUID
│   └── shared/               # Value objects (Amount, Date, etc.)
│
├── interface/                # Ports (traits/interfaces)
│   ├── email/                # EmailSender trait
│   └── persistence/          # UserRepository, AccountRepository traits
│
├── application/              # Application layer (use cases, services)
│   ├── gateway/              # CQRS, orchestration, transaction boundaries
│   └── services/             # UserService, AccountService, EmailService
│
├── infrastructure/           # Adapters (infra implementations)
│   ├── persistence/          # Postgres/sqlx repos
│   ├── messaging/            # Websocket, Kafka, etc.
│   └── external/             # Third-party integrations
│       ├── google_mail/      # Google API wrapper implementing EmailSender
│       ├── stripe_payments/  # Stripe integration
│       └── slack_webhooks/   # Slack notifications
│
├── server/                   # Axum runtime (HTTP entrypoint)
│   ├── src/
│   │   ├── main.rs           # Axum setup, AppState wiring
│   │   ├── routes.rs         # Axum routes
│   │   └── handlers.rs       # Request handlers (thin, delegate to services)
│   └── Cargo.toml
│
├── cli/                      # Clap runtime (CLI entrypoint)
│   ├── src/
│   │   ├── main.rs           # Clap setup, command wiring
│   │   └── commands.rs       # CLI commands (thin, delegate to services)
│   └── Cargo.toml
│
├── shared/                   # Cross-cutting libs
│   ├── kernel/               # error types, Result<T>, event traits
│   └── utils/                # serialization, logging, config loaders
│
└── tests/                    # Integration & E2E tests
    ├── db_tests/             # Runs against Postgres/sqlx
    └── e2e_tests/            # CLI + server combined tests

```

# Research

```
- Repository pattern when dealing with the DB, always

- Code is split into ‘domain’ and ‘infrastructure’ - the latter is where all inputs to the domain (e.g. route handlers, Kafka listeners etc) go along with the persistence layer and clients that interact with systems outside the app. Some people prefer to split these into domain/infrastructure/application (I.e. handlers)

- Ports and adapters architecture where the domain crate *does not* import anything from outside (excl cargo installed crates). This requires some explanation: it means the domain solely interacts with things outside of it using traits it defines. These traits are then implemented in the infrastructure/application crates. The latter have knowledge of the domain but vice versa is not true.

This also decouples any data models used for persistence (e.g. ORM models) from their domain models. In practice this looks like having a User struct in domain that often has the same fields as the User struct in the DB layer. I implement the From<DomainUser> trait for DbUser to convert between the two.
```

## Domain

- Traits to define the ports
- Value types
- Business logic (depending on value types & "ports" only)

Outside domain:

- "Concrete" adapters that implement the traits/ports
- (so far) dependency injection when running the business logic/domain code

Error handling

- For rich error handling - domain errors are just a subset of the errors that can be produced/handled. (though the domain will only ever know about domain errors)
