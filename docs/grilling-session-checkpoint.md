# Grilling Session Checkpoint

**Status:** Complete on 16 August 2026

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
- Disaster recovery preserves the Installation Identity but opens a new globally unique Recovery Epoch and records the restore boundary and Recovery Gap acknowledgement.
- External artifacts and Operations Evidence use a Fully Qualified Evidence Identity combining Installation Identity, Recovery Epoch, and the human-readable reference; short sequential references are unique only within an epoch.
- An intact Snapshot Artifact absent from the restored database is reported as external evidence not present in recovered history; it is verified without retention and never imported or used to reconstruct domain history.
- A passing restore test deletes its temporary stack and volume immediately; a failed environment may be retained in isolation for diagnosis only with a reason and a maximum seven-day automatic expiry.
- Release discovery occurs only through an explicit operator-invoked Update Check against official GitHub Releases; V1 performs no background check, download, or installation.
- Every release and Recovery Kit has a signed manifest covering Compose definitions, scripts, OCI image digests, and checksums; tooling verifies it with a pinned trusted public key and fails closed without an online verification dependency.
- V1 uses one long-lived offline key as both the pinned release trust anchor and the manual release-signing key, accepting harder rotation and a larger blast radius in exchange for simpler personal-release operations.
- The private signing key is an encrypted file with two offline copies in separate locations and a separately held passphrase; signing occurs only in a network-disconnected environment, never on the development host or in CI.
- Planned signing-key rotation uses a dual-signed transition and explicit fingerprint review; suspected compromise or unplanned loss uses the authenticated Operations interface with password step-up, independently verified media, fingerprint review, a reason, and an Administrative Audit Event. It cannot bootstrap trust from GitHub or the suspect key.
- Emergency Trust Reset is available only through a loopback browser connection on the application host and cannot be enabled through the private-network, VPN, or reverse-proxy profile.
- After a trust reset, the installed release may keep running, uninstalled artifacts signed only by the retired key are quarantined, and affected Recovery Kits require known-good offline review plus a new signed manifest before automated recovery use.
- First-run bootstrap uses interactive `setup.ps1` to validate the supported Windows/Docker environment, generate Installation Identity and ACL-protected secrets outside the repository, start the stack, and show the one-time owner setup token only in the console.
- Host PowerShell tools exclusively own network, proxy, secret, backup, and release-trust configuration; the web application owns Workspace and product-planning settings and only displays effective non-secret host state, apart from the constrained Emergency Trust Reset exception.
- Host changes use `config.ps1` with validation, redacted plan, explicit confirmation, protected previous configuration, stack health checks, and automatic configuration rollback when the new stack fails.
- `uninstall.ps1` removes runtime containers and network while preserving all data and recovery material; destructive `purge.ps1` is separate, enumerates exact targets, requires typed Installation Identity, and requires a final backup or explicit waiver.
- Purge deletes only canonicalized, installation-owned local assets and emits a redacted deletion report; it never deletes off-host backups, Recovery Kits, or private recovery and release-signing keys.
- V1 supports exactly one installation per Windows host; setup rejects a second instance, while the single Workspace continues to support multiple Products.

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

The Product Owner selected A when the session resumed.

## Session completion

The V1 grilling phase is complete with no unanswered decision required before specification and implementation planning. The Product Owner approved final document review plus commit and push; no application implementation is part of this session.
