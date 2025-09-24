# Better DDD Architecture

Repo: TBD

# Proposed directory tree

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

# Initial pain points
