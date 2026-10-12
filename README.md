# roku — encrypted backup

Encrypted (AES-256-CBC via openssl, PBKDF2) copy of:
- every sqlite database
- uploaded/stored files (uploads, instance, storage, contracts — not logs)
- .env

Decrypt with the passphrase in the server's /root/backup-secrets/passphrase.txt:

```bash
openssl enc -d -aes-256-cbc -pbkdf2 -in backup.tar.gz.enc -out backup.tar.gz -pass file:passphrase.txt
tar xzf backup.tar.gz
```

Or just run `scripts/restore.sh backup.tar.gz.enc` from the site's own repo, which does this for you.

Last backup (UTC): see LAST_BACKUP_UTC.txt. Source host: see HOST.txt.
Not stored here: application source (that's in the main repo), admin phones/login codes beyond what's in .env.
