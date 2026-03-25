# VDI Tool Exe for SQL Server Snapshot Backup and Restore

> A command-line tool that uses the SQL Server Virtual Device Interface (VDI) to perform `BACKUP` and `RESTORE` operations `WITH SNAPSHOT` on SQL Server databases.

## Prerequisites

- **SQL Server** must be installed on the target Azure VM.
- **SQLVDI.DLL** must be registered on the machine (included with SQL Server installations).
- **Windows NT Authentication** (Trusted Connection) is used to connect to SQL Server. Ensure the user running the tool has the necessary SQL Server permissions.

## Usage

```
snapshot {B|R} <databaseName> [options...]
```

### Backup

Perform a snapshot backup of a database:

```cmd
snapshot.exe B <sourceDB> [<instanceName>] [<dumpFile>]
```

**Examples:**

Back up database `MyDB` on the default SQL Server instance:
```cmd
snapshot.exe B MyDB
```

Back up database `MyDB` on a named instance `SQL2019`:
```cmd
snapshot.exe B MyDB SQL2019
```

Back up database `MyDB` to a specific dump file:
```cmd
snapshot.exe B MyDB SQL2019 mybackup.dmp
```

### Restore

Restore a database from a snapshot backup:

**Simple restore** (restore to the same database):
```cmd
snapshot.exe R <sourceDB> [<instanceName>] [<dumpFile>]
```

**Restore with MOVE** (restore to a different database with relocated files, this command should be used in the case the snapshot disks are attached):
```cmd
snapshot.exe R <sourceDB> <instanceName> <targetDB> <logicalDataName> <logicalLogName> <dataFile.mdf> <logFile.ldf> <dumpFile>
```

**Examples:**

Restore database `MyDB` on the default instance:
```cmd
snapshot.exe R MyDB
```

Restore database `MyDB` to a new database `MyDB_Restored` with relocated files:
```cmd
snapshot.exe R MyDB SQL2019 MyDB_Restored MyDB_Data MyDB_Log "D:\Data\MyDB_Restored.mdf" "D:\Logs\MyDB_Restored.ldf" mybackup.dmp
```

### Parameters

| Parameter | Description |
|---|---|
| `B` or `R` | Operation type: **B** for Backup, **R** for Restore |
| `sourceDB` | Name of the source database to back up or restore from |
| `instanceName` | SQL Server named instance (omit for the default instance) |
| `targetDB` | Target database name for restore with MOVE |
| `logicalDataName` | Logical name of the data file in the backup |
| `logicalLogName` | Logical name of the log file in the backup |
| `dataFile` | Physical path for the restored data file (.mdf) |
| `logFile` | Physical path for the restored log file (.ldf) |
| `dumpFile` | Path to the dump file (default: `snapshot.dmp`) |

## Interactive Prompts

During execution, the tool will prompt you at key stages of the snapshot process:

- **Backup:** After SQL Server freezes the database, you will be prompted to take the disk snapshot. Confirm once the snapshot is complete.
- **Restore:** You will be prompted to mount the snapshot before the restore proceeds. Confirm once the snapshot is mounted.

At each prompt, you can choose to abort the operation if needed.

## Notes

- The restore operation uses `NORECOVERY` and `REPLACE` options when performing a restore with MOVE.