# Reject stale domain commands with optimistic concurrency

Every update and lifecycle-transition command will carry the record version on which the Product Owner made the decision and will be rejected if that version is no longer current. The application will not use last-write-wins, field-level automatic merge, or pessimistic editing locks because domain fields participate in shared invariants and a stale screen must not overwrite a newer product decision.

Pure creation and append-only commands do not overwrite an earlier representation, but they must validate all relevant invariants against the latest committed state inside their transaction. A command will never retry automatically after a concurrency conflict when doing so could reinterpret the owner's original intent.

On conflict, the interface will preserve the attempted input temporarily and compare it with the current committed values so the owner can deliberately reapply selected changes to the latest version. Lifecycle transitions must be confirmed again, and V1 will provide no force-overwrite escape hatch.

Editable forms will keep a tab-scoped recovery draft in browser session storage rather than create partial domain records on the server. A recovered draft will be disclosed before reuse and will be removed after a successful save, logout, or tab closure; password, token, and authentication forms are never eligible for draft storage.

High-impact commands will use a dedicated review step rather than a generic confirmation dialog. The review will expose affected records, validation results, consequences, user-facing changes, and the recorded reason before an explicitly named action is submitted against the latest record versions.

A command is high impact when it enters a terminal lifecycle state, creates or revises a user-facing commitment or publication, archives or restores a Product, imports or exports a Workspace, or changes security or operational configuration. This category rule applies to later commands with the same consequences without requiring a separately maintained action-name list; ordinary edits do not receive a review step.
