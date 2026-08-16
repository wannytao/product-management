# Grilling Session Checkpoint

**Status:** Resumed on 16 August 2026

## Latest resolved decisions

- PostgreSQL data persists in a Docker named volume and is backed up using PostgreSQL-aware tools rather than live file copies.
- The supported scheduler is a bundled PowerShell script invoked by Windows Task Scheduler.
- Each scheduled run produces a PostgreSQL custom-format backup, checksum, seven-daily and four-weekly retention, and a narrowly scoped result for the Operations page.
- The off-host artifact is encrypted while being produced using an `age`-compatible public-key recipient, without a plaintext staging file.
- The application host retains only the public recipient; the private recovery key remains outside the host, database, and Workspace export.
- The private recovery key has two offline copies in separate locations. Backup readiness requires a successful encrypt/decrypt test, the system retains only the key fingerprint and acknowledgement, and quarterly restore tests exercise the real key.
- Restore targets a new named volume, validates the recovered database while the application is stopped, and requires explicit Workspace Owner confirmation before cutover. The previous volume is retained for a separately governed rollback or deletion decision.
- A rollback volume becomes eligible for explicit, audited deletion only after seven days of stable post-cutover use and one successful encrypted post-cutover backup; it is never deleted automatically.
- Restore verification runs quarterly in an isolated temporary stack on the supported host, with a complete replacement-host rehearsal at least annually and whenever the recovery architecture materially changes.
- A backup is restored and validated with its recorded application release and PostgreSQL major before any separately controlled upgrade is attempted; restore and schema migration are never combined.
- Every installed release has an off-host Recovery Kit with Compose definitions, scripts, runbook, checksums, and offline OCI images, retained while any backup references it and containing no secrets or private recovery key.
- Every restore revokes all restored sessions. If the original pepper is unavailable, replacement-host recovery generates a new pepper and requires a local CLI reset of the Workspace Owner password.
- Every cutover requires a Recovery Gap Summary and explicit acknowledgement of the maximum interval of potentially missing changes, even when the selected backup is inside the 24-hour RPO.

## Resolved checkpoint question

How should the private recovery key be controlled?

**A — Two offline copies in separate locations, tested before scheduling (selected)**

Keep two encrypted removable-media or equivalent offline copies in separate locations. Store only the key fingerprint in the system, require a successful encrypt/decrypt test before backup readiness, and use the real key during quarterly restore tests.

**B — One offline copy**

Simpler custody, but the key remains a single point of failure.

**C — Windows Credential Manager on the application host**

Convenient restoration, but weakens separation between the encrypted backup and its recovery capability and may fail with the host.

**D — Include the private key in the Workspace export**

Portable, but creates circular recovery dependency and exposes backups when an export leaks.

The Product Owner selected A when the session resumed. This checkpoint no longer contains an unanswered question; the live grilling session continues from the restore workflow.
