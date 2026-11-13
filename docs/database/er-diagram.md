---
title: ER Diagram
description: archiviart-core database ER diagram
---

# archiviart-core — ER Diagram

Entity-Relationship diagram of the PostgreSQL schema created by the
`archiviart-core` migration (`modules/service/cmd/main.go`, GORM `AutoMigrate`
on the models in `modules/service/data/dbmodel.go`).

Column names follow GORM's default snake_case naming; types are the ones GORM
generates on PostgreSQL.

<div align="center" markdown="1">
```mermaid
erDiagram
    file_metadata {
        uuid        file_id     PK "default gen_random_uuid()"
        text        user_id        "nullable"
        text        file_name
        text        file_hash
        jsonb       meta
        timestamptz uploaded_at
    }

    dossiers {
        text        dossier_name        PK
        text        dossier_description    "nullable"
        timestamptz created_at
    }

    dossier_proofs {
        text        proof_hash          PK
        text        previous_proof_hash FK "nullable, self reference"
        jsonb       proof_data
        timestamptz created_at
        text        dossier_name        FK "not null, indexed"
    }

    dossier_proof_file_metadata {
        text proof_hash PK,FK
        uuid file_id    PK,FK
    }

    available_metadata_types {
        text type_value PK
    }

    user_metadata {
        uuid    id          PK "default gen_random_uuid()"
        text    user_id        "indexed, '*' = all users"
        text    key
        boolean required
        text    description    "nullable"
        text    type_value  FK
    }

    event_statuses {
        text status PK
    }

    event_types {
        text type_value PK
    }

    triggered_events {
        uuid        id                 PK "default gen_random_uuid()"
        text        event_type_value   FK
        text        event_status_value FK "indexed"
        text        user_id               "nullable"
        jsonb       payload
        bigint      attempts
        bigint      max_attempts          "default 5"
        timestamptz created_at
        timestamptz updated_at
    }

    dossiers                 ||--o{ dossier_proofs              : "has proofs"
    dossier_proofs           |o--o| dossier_proofs              : "previous proof"
    dossier_proofs           ||--o{ dossier_proof_file_metadata : "certifies"
    file_metadata            ||--o{ dossier_proof_file_metadata : "certified by"
    available_metadata_types ||--o{ user_metadata               : "types"
    event_types              ||--o{ triggered_events            : "classifies"
    event_statuses           ||--o{ triggered_events            : "status of"
```
</div>

## Notes

- **Append-only tables**: `dossiers`, `dossier_proofs` and
  `dossier_proof_file_metadata` have a `<table>_append_only` trigger that calls
  `prevent_mutation()` and rejects `UPDATE`, `DELETE` and `TRUNCATE`.
- **Proof chain**: each `dossier_proofs` row points to the previous proof via
  `previous_proof_hash` (`NULL` for the first proof of the chain).
- **Proof ↔ file**: many-to-many through `dossier_proof_file_metadata`.
- **Lookup tables seeded by the migration**:
    - `available_metadata_types`: `TEXT`, `NUMBER`, `BOOLEAN`, `DATE`, `TIME`
    - `event_statuses`: `PENDING`, `PROCESSING`, `COMPLETED`, `FAILED`
    - `event_types`: `OCR`
    - `user_metadata`: one row `user_id = '*'`, `key = 'name'`, `required = true`, `type_value = 'TEXT'`
- **`user_id`** columns hold the Keycloak subject; there is no users table in
  the core database.
- **Not migrated yet** (commented out): `vendor_types`, `vendors`,
  `participants`, `items`, `showcases`, `file_raw_contents`.
