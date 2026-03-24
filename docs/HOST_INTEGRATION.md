# Host Integration Sketch

## Why This Exists

`graphql-orm-macros` generates code that currently expects a specific host-crate shape. To make the crate reusable across future projects, that contract needs to be explicit.

This document describes the current expectations and the next abstraction layer needed for:

- nested relation batching with arguments
- multi-database support
- cleaner long-term reuse

## Current Required Host Modules

Today the generated code assumes the consuming crate exposes:

- `crate::db::Database`
- `crate::graphql::auth::AuthExt`
- `crate::graphql::filters::*`
- `crate::graphql::pagination::*`
- `crate::graphql::orm::*`
- `crate::graphql::loaders::RelationLoader`

## Recommended Future Host Traits

### Database/Dialect Layer

```rust
pub trait SqlDialect {
    fn placeholder(&self, index: usize) -> String;
    fn current_timestamp_sql(&self) -> &'static str;
    fn quote_identifier(&self, ident: &str) -> String;
}

pub trait QueryExecutor {
    type Pool;
    type Row;
    type Error;
}
```

### Row And Value Layer

```rust
pub enum DbValue {
    String(String),
    Int(i64),
    Float(f64),
    Bool(bool),
    Bytes(Vec<u8>),
    Json(String),
    Null,
}
```

### Relation Batching Layer

The next substantial improvement for nested relation loading should introduce a relation-aware request key.

```rust
#[derive(Clone, Debug, Hash, PartialEq, Eq)]
pub struct RelationQueryKey {
    pub entity: &'static str,
    pub relation: &'static str,
    pub target: &'static str,
    pub where_signature: Option<String>,
    pub order_signature: Option<String>,
    pub page_signature: Option<String>,
}
```

This key is intended to group sibling nested GraphQL relation requests that share the same arguments.

### Batched Relation Loader

Conceptually, the host layer should evolve toward something like:

```rust
pub trait BatchedRelationLoader<T> {
    type ParentKey;

    async fn load_many(
        &self,
        query: RelationQueryKey,
        parent_keys: &[Self::ParentKey],
    ) -> Result<std::collections::HashMap<Self::ParentKey, Vec<T>>, anyhow::Error>;
}
```

For single-object relations, the returned vectors can be collapsed to `Option<T>`.

## Recommended Delivery Strategy

1. Keep the current `RelationLoader<T>` path for simple no-args batching.
2. Add `RelationQueryKey` support in the host integration layer.
3. Update generated resolvers to prefer the batched relation loader when available.
4. Use grouped fetch plus in-memory slicing first.
5. Upgrade to SQL-level per-parent pagination with window functions after the grouping interface is stable.

## Why The Loader Key Uses Signatures

The query key should not embed full typed GraphQL inputs as hash-map keys unless the entire host stack standardizes those types.

Instead, normalize:

- `Where`
- `OrderBy`
- `Page`

into deterministic signatures, then batch on those signatures. That keeps the loader contract simpler and decouples batching from individual generated input types.
