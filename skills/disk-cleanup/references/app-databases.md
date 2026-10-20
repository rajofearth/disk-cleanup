# App databases

Use this when a large SQLite database is a candidate in tier 4.

## Procedure

1. Identify the app and every service it runs. Close them. Do not compact a database that is open.
2. Check free space. VACUUM needs temporary space about the size of the database. Put the backup on another volume when possible.
3. Checkpoint the WAL, then copy the database to the backup.
4. Run `PRAGMA integrity_check;`. Stop if it does not return `ok`.
5. Record row counts for key tables.
6. Run `VACUUM;`.
7. Check integrity and row counts again.
8. Open the app and verify history, settings, and sessions. Keep the backup until it works.

## Commands

```sh
sqlite3 app.db "PRAGMA wal_checkpoint(TRUNCATE);"
sqlite3 app.db "PRAGMA integrity_check;"
sqlite3 app.db "SELECT COUNT(*) FROM table_name;"
sqlite3 app.db "VACUUM;"
sqlite3 app.db "PRAGMA integrity_check;"
```

## Stop conditions

- The app or service will not close.
- The backup does not fit.
- Integrity check fails before VACUUM.
- Key table row counts change after VACUUM.
- The app does not open or loses history.

Restore the backup if any of these happen.

Some app databases are encrypted or proprietary. Use the app's own maintenance command instead of `sqlite3`.
