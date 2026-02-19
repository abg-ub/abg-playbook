# PostgreSQL Backup & Restore Using Docker (`pg_dump` & `pg_restore`)

This document provides a **complete, reusable guide** for backing up and
restoring a PostgreSQL database running inside a Docker container.

It is designed to be:

-   Generic (works for any PostgreSQL container)
-   Safe for production usage
-   Easy to copy into any GitHub repository as documentation

------------------------------------------------------------------------

## Use Cases

-   VPS / server migration
-   Full database backup
-   Disaster recovery
-   Database cloning
-   PostgreSQL version upgrades

------------------------------------------------------------------------

## Requirements

-   Docker installed on the host machine
-   A running PostgreSQL Docker container (optional)
-   PostgreSQL tools available inside the container:
    -   `pg_dump`
    -   `pg_restore`
-   Valid PostgreSQL credentials

------------------------------------------------------------------------

## Terminology & Dynamic Inputs

Replace the placeholders below with your own values:

  Placeholder          Description             Example
  -------------------- ----------------------- ---------------------
  `<container_name>`   Docker container name   `keycloak-postgres`
  `<db_user>`          PostgreSQL username     `keycloak`
  `<db_name>`          Database name           `keycloak`
  `<backup_file>`      Backup file name        `keycloak.dump`

------------------------------------------------------------------------

## Backup Database (Export)

Creates a **compressed custom-format backup** of the database.

``` bash
docker exec -i <container_name>   pg_dump -U <db_user> -F c <db_name> > <backup_file>
```

### Example

``` bash
docker exec -i keycloak-postgres   pg_dump -U postgres_user -F c postgres_db > postgres_backup.dump
```

------------------------------------------------------------------------

## Restore Database (Import)

``` bash
docker exec -i <container_name>   pg_restore -U <db_user> -d <db_name> --clean --if-exists < <backup_file>
```

### Example

``` bash
docker exec -i keycloak-postgres   pg_restore -U keycloak -d keycloak --clean --if-exists < keycloak.dump
```

------------------------------------------------------------------------

## Copy Backup Files Between Host and Docker Container (`docker cp`)

In some cases, you may want to:

-   Copy a backup **from your local machine into the container**
-   Copy a backup **from the container to your local machine**
-   Move files without using STDIN/STDOUT redirection

Docker provides the `docker cp` command for this purpose.

------------------------------------------------------------------------

### Copy File From Local Machine → Docker Container

``` bash
docker cp <local_file> <container_name>:<container_path>
```

#### Example

``` bash
docker cp keycloak.dump keycloak-postgres:/tmp/keycloak.dump
```

Restore it from inside the container:

``` bash
docker exec -it keycloak-postgres   pg_restore -U keycloak -d keycloak --clean --if-exists /tmp/keycloak.dump
```

------------------------------------------------------------------------

### Copy File From Docker Container → Local Machine

``` bash
docker cp <container_name>:<container_path> <local_destination>
```

#### Example

``` bash
docker cp keycloak-postgres:/tmp/keycloak.dump ./keycloak.dump
```

------------------------------------------------------------------------

## When to Use `docker cp` vs Redirection

### Use STDIN/STDOUT (Recommended for Most Cases)

``` bash
docker exec -i <container_name> pg_dump ... > <backup_file>
```

✔ Simpler\
✔ No temporary files inside container\
✔ Ideal for automation

------------------------------------------------------------------------

### Use `docker cp` When:

-   You already generated a backup inside the container
-   You need to inspect the dump file inside the container
-   You want clearer separation of steps
-   You are debugging restore issues

------------------------------------------------------------------------

## Optional: Verify Backup File Inside Container

``` bash
docker exec -it <container_name> ls -lh /tmp
```

------------------------------------------------------------------------

## Warnings

-   This operation **overwrites existing data**
-   Stop application services before restoring
-   Always test restores in staging first

------------------------------------------------------------------------

## Best Practices

-   Use custom format (`-F c`)
-   Store backups off-server
-   Automate backups
-   Test restore procedures
