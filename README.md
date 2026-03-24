# `graphql-orm-macros`

Procedural macros for generating GraphQL entity types, relation resolvers, CRUD operations, and schema roots for an `async-graphql` + ORM-style backend.

## Status

This crate is being extracted from a standalone macro file into a reusable Rust crate intended for future projects.

Current priorities:

- package the macro as a proper proc-macro crate
- reduce SQLite-specific assumptions
- improve nested relation batching to avoid N+1 queries
- prepare for PostgreSQL, MySQL, and SQL Server support

## Included Macros

- `mutation_result!`
- `#[derive(GraphQLEntity)]`
- `#[derive(GraphQLRelations)]`
- `#[derive(GraphQLOperations)]`
- `schema_roots!`

## Current Coupling

The macro has been made project-generic, but it is not yet framework-neutral. The generated code still expects a host crate to provide:

- `crate::graphql::orm::*`
- `crate::graphql::filters::*`
- `crate::graphql::pagination::*`
- `crate::graphql::auth::AuthExt`
- `crate::db::Database`

That host interface will be documented and progressively generalized as the crate evolves.

## Near-Term Roadmap

1. Package the macro cleanly as a standalone crate.
2. Improve relation batching for nested queries with filters, sorting, and pagination.
3. Introduce SQL dialect abstractions for SQLite, PostgreSQL, MySQL, and SQL Server.
4. Add examples, tests, and reusable integration docs.

## Development

```bash
cargo check
```

## Planned Feature Flags

- `sqlite`
- `postgres`
- `mysql`
- `mssql`

These are scaffolding flags at the moment. Backend-specific behavior still needs to be extracted from the generated code and host integration layer.

## License

License has not been selected yet.
