# Roku — final encrypted archive

The Roku restaurant closed in October 2026 and its site was taken offline and removed from the VPS.
This private repository holds the **final, encrypted** copy of everything the server had for it.
The code lives in the sibling private repository `iliaasadi/roku` (nothing secret is stored there).

Archive date (UTC): see `LAST_BACKUP_UTC.txt`.

| File | What it contains |
|---|---|
| `backup.tar.gz.enc` | The last automatic nightly backup bot archive (12 Oct 2026): databases, uploads, `.env`. |
| `roku.db.enc` | The final SQLite database (menu, categories, customers, orders, settings). Contains customer phone numbers, so it is encrypted. |
| `roku-server-full.tar.gz.enc` | The whole `/opt/vps/roku` and `/opt/vps/roku-db` directories as they were on the server: application code, `.env`, `instance/` (database), `static/uploads/` (images), `logs/`, earlier full backups, and the server-side `.git`. |
| `roku-server-state.tar.gz.enc` | Server configuration for Roku: the shared Caddy edge config and the Roku site block, docker-compose snapshot, container / network / volume inspect output, the server git bundle, cron, ssh-config notes, row counts. |

The Docker image (`roku-app:local`, ~140 MB) is not stored on GitHub (file size limit). It is fully rebuildable from the `Dockerfile` in the code repository.

## Encryption

AES-256-CBC with PBKDF2 via `openssl`, using the same passphrase file as all the other `*-db` backup repositories
(on the server: `/root/backup-secrets/passphrase.txt`; keep a copy in a password manager).

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in roku.db.enc -out roku.db -pass file:passphrase.txt
openssl enc -d -aes-256-cbc -pbkdf2 -in roku-server-full.tar.gz.enc -out roku-server-full.tar.gz -pass file:passphrase.txt
openssl enc -d -aes-256-cbc -pbkdf2 -in roku-server-state.tar.gz.enc -out roku-server-state.tar.gz -pass file:passphrase.txt
```

## Restore the site

1. `git clone git@github.com:iliaasadi/roku.git && cd roku`
2. Decrypt `roku-server-full.tar.gz.enc`, copy `.env` from it (or create one from `.env.example`), and put the decrypted `roku.db` at `instance/roku.db`.
3. Copy `static/uploads/` from the decrypted archive.
4. `docker compose up -d --build` (the app listens on port 8004; route a domain to it with the Caddy block in `roku-server-state`).

