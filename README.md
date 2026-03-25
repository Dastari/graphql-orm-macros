# `graphql-orm-macros`

Procedural macros for generating GraphQL entity types, relation resolvers, CRUD operations, and schema roots for an `async-graphql` + ORM-style backend.

## Status

This crate now targets the `graphql-orm` runtime crate directly instead of expecting application-local host modules.

Use it with:

- `graphql-orm`
- `graphql-orm-macros`

## Included Macros

- `mutation_result!`
- `#[derive(GraphQLEntity)]`
- `#[derive(GraphQLRelations)]`
- `#[derive(GraphQLOperations)]`
- `schema_roots!`

## Near-Term Roadmap

1. Expand runtime metadata and migration support in `graphql-orm`.
2. Improve relation batching for nested queries with filters, sorting, and pagination.
3. Introduce full SQL dialect abstractions for SQLite, PostgreSQL, MySQL, and SQL Server.
4. Add examples and integration docs for application crates.

## Development

```bash
cargo check
```

## Planned Feature Flags

- `sqlite`
- `postgres`
- `mysql`
- `mssql`

Exactly one backend flag must be enabled at a time. The selected flag now controls the generated runtime pool and row aliases:

- `sqlite` -> `graphql_orm::DbPool`, `graphql_orm::DbRow`
- `postgres` -> `graphql_orm::DbPool`, `graphql_orm::DbRow`
- `mysql` -> planned
- `mssql` -> planned

SQLite and PostgreSQL are covered by live integration tests through `graphql-orm`. MySQL and SQL Server remain planned.

## License

License has not been selected yet.
