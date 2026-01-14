# PostgreSQL Backup & Restore Using Docker (`pg_dump` & `pg_restore`)

This document provides a **complete, reusable guide** for backing up and restoring a PostgreSQL database running inside a Docker container.

It is designed to be:

- Generic (works for any PostgreSQL container)
- Safe for production usage
- Easy to copy into any GitHub repository as documentation

---

## Use Cases

- VPS / server migration
- Full database backup
- Disaster recovery
- Database cloning
- PostgreSQL version upgrades

---

## Requirements

- Docker installed on the host machine
- A running PostgreSQL Docker container
- PostgreSQL tools available inside the container:
  - `pg_dump`
  - `pg_restore`
- Valid PostgreSQL credentials

---

## Terminology & Dynamic Inputs

Replace the placeholders below with your own values:

| Placeholder        | Description           | Example             |
| ------------------ | --------------------- | ------------------- |
| `<container_name>` | Docker container name | `keycloak-postgres` |
| `<db_user>`        | PostgreSQL username   | `keycloak`          |
| `<db_name>`        | Database name         | `keycloak`          |
| `<backup_file>`    | Backup file name      | `keycloak.dump`     |

---

## Backup Database (Export)

Creates a **compressed custom-format backup** of the database.

```bash
docker exec -i <container_name> \
  pg_dump -U <db_user> -F c <db_name> > <backup_file>
```

### Example

```bash
docker exec -i keycloak-postgres \
  pg_dump -U postgres_user -F c postgres_db > postgres_backup.dump
```

---

## Restore Database (Import)

```bash
docker exec -i <container_name> \
  pg_restore -U <db_user> -d <db_name> --clean --if-exists < <backup_file>
```

### Example

```bash
docker exec -i keycloak-postgres \
  pg_restore -U keycloak -d keycloak --clean --if-exists < keycloak.dump
```

---

## Warnings

- This operation **overwrites existing data**
- Stop application services before restoring
- Always test restores in staging first

---

## Best Practices

- Use custom format (`-F c`)
- Store backups off-server
- Automate backups
- Test restore procedures
