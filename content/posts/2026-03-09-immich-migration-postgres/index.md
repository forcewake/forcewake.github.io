---
title: "The Great Immich Migration: From v1.113.0 to v2.5.6"
date: "2026-03-09T09:00:00Z"
slug: "immich-migration-postgres-14-to-18"
url: "/immich-migration-postgres-14-to-18/"
description: >
  Upgrading a self-hosted Immich instance from v1.113.0 to v2.5.6 on Unraid sounded routine until the
  TypeORM-to-Kysely migration, the pgvecto.rs to VectorChord transition, and a shared PostgreSQL 14 to 18 move
  turned it into a five-round database recovery exercise.
cover:
  image: "cover.png"
  relative: true
ShowToc: true
TocOpen: false
ShowReadingTime: true
comments: true
tags:
  - Immich
  - PostgreSQL
  - Unraid
  - Docker
  - Self-Hosting
  - VectorChord
categories:
  - self-hosting
  - databases
---

*How a "simple" photo library upgrade turned into a deep dive through PostgreSQL version migrations, deprecated vector extensions, and the kind of database surgery you hope to never need.*

---

## The Starting Point

My Immich instance had been happily humming along at v1.113.0 for months on my Unraid server. I was running the community all-in-one `imagegenius/immich` container variant, which bundles the server, microservices, machine learning, and Redis into one image, backed by an NVIDIA GPU for CUDA-accelerated ML and a shared PostgreSQL 14 instance that also served a pile of other workloads.

It was time to upgrade. How hard could it be?

One detail mattered more than I expected: Immich does support [pre-existing PostgreSQL](https://immich.app/docs/administration/postgres-standalone/#using-vectorchord-instead-of-pgvectorsrs), but the docs treat it as an advanced path rather than the default recommendation. I was firmly in that advanced-path territory.

## The Research Phase: What Happened to Immich?

A lot, as it turns out. In the time since v1.113.0:

- [Immich joined FUTO](https://immich.app/blog/immich-joins-futo) in May 2024, which let the core team work on the project full-time.
- The project moved to AGPLv3 in early 2024, then introduced optional [product keys](https://immich.app/blog/immich-product-keys) in July 2024 ($25 individual, $100 server).
- The vector-search transition started with [v1.133.0](https://github.com/immich-app/immich/releases/tag/v1.133.0) in May 2025, where `pgvecto.rs` began giving way to VectorChord.
- [v1.137.0](https://github.com/immich-app/immich/releases/tag/v1.137.0) in July 2025 removed TypeORM in favor of Kysely, and Immich's [upgrade guidance](https://immich.app/docs/administration/errors#typeorm-upgrade) requires first landing on v1.132.0 or higher before crossing that boundary.
- [Immich v2.0.0](https://github.com/immich-app/immich/releases/tag/v2.0.0) landed on October 1, 2025 and locked in semver going forward.

At the time of writing on March 9, 2026, the current stable release was [v2.5.6](https://github.com/immich-app/immich/releases/tag/v2.5.6).

That meant **no direct jump** from v1.113.0 to latest. The upgrade path had to be:

```text
v1.113.0 -> v1.132.3 -> v2.5.6
```

## The Ambition: Why Not PG18?

Here's where I made things interesting. I'd already migrated my other databases from PG14 to PG18 in a previous session. The Immich database was the only holdout, stuck on PG14 because it used `pgvecto.rs` instead of `pgvector`.

But Immich's current PostgreSQL guidance includes [VectorChord as the replacement path](https://immich.app/docs/administration/postgres-standalone/#using-vectorchord-instead-of-pgvectorsrs), and VectorChord has pre-built `.deb` packages for PG18. So why not migrate the database and upgrade Immich in one go?

The plan seemed clean:

1. Add VectorChord to my custom PG18 Docker image.
2. Dump the Immich database from PG14.
3. Restore it to PG18.
4. Upgrade the Immich container.

What could go wrong?

## Round 1: The Dockerfile Dance

My PG18 already runs a custom Docker image with `pgvector` and `Apache AGE` baked in. Adding VectorChord meant downloading a `.deb` from GitHub releases. First attempt:

```dockerfile
RUN apt-get update && \
    apt-get install -y curl && \
    curl -L -o /tmp/vchord.deb \
      "https://github.com/tensorchord/VectorChord/releases/download/1.1.1/postgresql-18-vchord_1.1.1-1_amd64.deb" && \
    dpkg -i /tmp/vchord.deb
```

**Result**: `curl: (77) error setting certificate file`

The minimal Postgres Docker image didn't include `ca-certificates`. A one-line fix, but the kind of thing that burns five minutes when you're not expecting it.

## Round 2: The TypeORM Trap

With VectorChord installed, I dumped the Immich database from PG14, restored it to PG18, and launched [v1.132.3](https://github.com/immich-app/immich/releases/tag/v1.132.3) for the intermediate TypeORM migration.

**Result**: `Error: The pgvecto.rs extension is not available in this Postgres instance.`

Of course. v1.132.3 still expects `pgvecto.rs`. It needs to run against PG14, not PG18. The VectorChord transition does not start until the later migration path.

**The fix**: Run v1.132.3 against the original PG14 database. Let it complete the TypeORM -> Kysely migration there. Then re-dump the migrated database to PG18.

## Round 3: The Dump Format Wars

After v1.132.3 ran its migrations on PG14, I re-dumped to PG18 using a text-format pipe:

```bash
pg_dump -U postgres immich | psql -U postgres -d immich
```

**Result**: `there is no unique constraint matching given keys for referenced table "users"`

The text-format dump was fragile. When the `CREATE EXTENSION vectors` statement failed because `pgvecto.rs` doesn't exist on PG18, the error cascaded. Subsequent statements that depended on the extension's success were silently broken. Constraints weren't created. Foreign keys were missing.

**The fix**: Switch to custom format (`pg_dump -Fc`) and `pg_restore`. The custom format handles dependency ordering properly and keeps the rest of the schema intact even when a few extension-specific objects fail:

```bash
pg_dump -Fc --no-owner --no-privileges immich > /tmp/immich.dump
pg_restore -d immich --no-owner --no-privileges /tmp/immich.dump
```

I still got 12 errors from the missing `vectors` extension, but those were expected and harmless.

## Round 4: The Missing Migration Record

With the proper restore done, I launched Immich latest.

**Result**: `Migrations failed: Error: Invalid upgrade path`

The Kysely `InitialMigration` record was missing from PG18. Why? Because v1.132.3 wrote that record to PG14 during the intermediate stop, and my earlier dump had captured the pre-migration state.

**The fix**: One SQL statement:

```sql
INSERT INTO kysely_migrations (name, timestamp)
VALUES ('1744910873969-InitialMigration', '2026-03-08T23:35:27.032Z');
```

## Round 5: The Ghost Tables

Next launch: all 50+ Kysely migrations ran successfully. Then this showed up:

**Result**: `relation "clip_index" does not exist`

The `smart_search` and `face_search` tables were gone. Those tables stored vector embeddings using `pgvecto.rs`'s custom `vector` type. During the dump/restore, they could not be created because that type doesn't exist on PG18.

The migration system thought they existed because the migrations were marked as done, but the actual tables were missing.

An important subtlety here: Immich v2.x also renamed many tables to singular form during its migrations, so the foreign-key references needed to point to the new names.

**The fix**: Manually create the tables with pgvector's standard `vector(512)` type:

```sql
CREATE TABLE smart_search (
  "assetId" uuid NOT NULL PRIMARY KEY,
  embedding vector(512) NOT NULL,
  FOREIGN KEY ("assetId") REFERENCES asset(id) ON DELETE CASCADE
);

CREATE TABLE face_search (
  "faceId" uuid NOT NULL PRIMARY KEY,
  embedding vector(512) NOT NULL,
  FOREIGN KEY ("faceId") REFERENCES asset_face(id) ON DELETE CASCADE
);
```

## The Finish Line

With the ghost tables created and VectorChord enabled, Immich v2.5.6 finally started:

```text
[Api:DatabaseRepository] Finished running migrations
[Api:Bootstrap] Immich (Api) is running [v2.5.6] [production]
[Microservices:Bootstrap] Immich Microservices is running [v2.5.6] [production]
[Microservices:MachineLearningRepository] Machine learning server became healthy
```

The API confirmed:

```json
{
  "major": 2,
  "minor": 5,
  "patch": 6,
  "smartSearch": true,
  "facialRecognition": true,
  "duplicateDetection": true
}
```

## What I Learned

### 1. Text-format `pg_dump` is dangerous for complex schemas

If your database has extensions, custom types, or complex dependency chains, always use `pg_dump -Fc` with `pg_restore`. The text-format pipe executes everything sequentially, and one failed `CREATE EXTENSION` can quietly poison the downstream schema.

### 2. Intermediate upgrade stops are real

Unlike simpler apps where you can jump across versions, both GitLab and Immich enforce intermediate stops. Immich's docs explicitly require a `v1.132.0+` stop before crossing into the Kysely world.

### 3. Migration state is not schema state

Just because a migration is marked as done in the migrations table doesn't mean the schema change actually succeeded. When you move databases between PostgreSQL instances with different extensions, you can end up with phantom migrations: records that say "done" while the corresponding tables don't exist.

### 4. `pgvecto.rs` -> VectorChord is a one-way door

The vector embeddings stored in `pgvecto.rs` format cannot be directly transferred to pgvector or VectorChord. The tables need to be recreated empty, and Immich will regenerate the embeddings through its ML pipeline. If you have a large library, that reprocessing can take a while.

### 5. Custom Docker images on Unraid work fine

Unraid doesn't natively expose Dockerfiles in its UI, but building a custom image on the server and pointing the container at it works perfectly well. The image survives reboots because Unraid recreates containers from templates, and local images don't need Docker Hub.

## The Final Architecture

```text
┌──────────────────────────────────────────────┐
│  Unraid Server (Tower, i9-9900K)             │
│                                              │
│  ┌────────────┐     ┌─────────────────────┐  │
│  │  Immich    │────▶│  PostgreSQL 18      │  │
│  │  v2.5.6    │     │  (port 6432)        │  │
│  │  CUDA ML   │     │                     │  │
│  │  port 3080 │     │  Extensions:        │  │
│  └────────────┘     │  • pgvector 0.8.2   │  │
│                     │  • VectorChord 1.1.1│  │
│  ┌────────────┐     │  • Apache AGE 1.7.0 │  │
│  │  17 other  │────▶│                     │  │
│  │  services  │     │  18 databases       │  │
│  └────────────┘     └─────────────────────┘  │
│                                              │
│  ┌─────────────────────────────────────────┐ │
│  │  PostgreSQL 14 (port 5432)              │ │
│  │  Can be retired - all DBs migrated      │ │
│  └─────────────────────────────────────────┘ │
└──────────────────────────────────────────────┘
```

What started as a "quick upgrade" became a five-round fight with database migrations, extension incompatibilities, and schema archaeology. But the end result is clean: everything consolidated on PG18 with modern extensions, and Immich running the latest stable release with CUDA acceleration.

Time to go look at some photos.
