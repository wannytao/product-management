# Roadmap and Backlog Management

Language used by a product-planning application for small-to-medium software product teams.

## Language

**Workspace**:
The top-level ownership boundary that groups and isolates one or more Products and their planning data.
_Avoid_: Product, Project, Folder

**Workspace Time Zone**:
The single IANA time zone selected during Workspace setup and used to determine the Workspace's current date and the end-of-day boundary for every date-based condition. Date-only values become overdue only after their local day ends; event timestamps are stored as UTC instants and displayed in the Workspace Time Zone. Changing the time zone does not rewrite existing date-only values or immutable timestamps. V1 evaluates every date-only value exactly as recorded, including dates on weekends or holidays, and has no business-calendar or automatic date-shifting behavior.
_Avoid_: Host Time Zone, Browser Time Zone, Per-Product Time Zone

**Workspace Time Zone Revision**:
An immutable record of a Workspace Time Zone change containing the previous and new time zones, the Workspace Owner's reason, actor, and time. Before confirmation, the owner previews derived conditions that may change; applying the revision immediately recalculates those conditions without rewriting stored date-only values or event timestamps.
_Avoid_: Date Revision, Timestamp Conversion, Per-Product Time Zone Change

**Workspace Owner**:
The authenticated user with full control over a Workspace and its access configuration; this access role is distinct from the Product Owner's product-decision responsibility.
_Avoid_: Product Owner, Anonymous Administrator

**Historical Actor**:
A non-authenticating identity reference that preserves the stable actor key and immutable display-name-at-event snapshot attached to imported domain revision history. Every revision continues to show the name recorded when its event occurred; changing a linked owner's current display name affects only later events and never rewrites earlier snapshots. A Historical Actor grants no access and contains no credential or session data; the owner of the receiving installation remains the actor for every change made after import.
_Avoid_: Disabled Account, Imported User, Workspace Owner, Credential

**Administrative Audit Event**:
An immutable record of a high-impact administrative or security action or attempt: Workspace Owner setup, authentication recovery or session revocation, Workspace export or import, and Workspace configuration revision. Security and administrative attempts are recorded whether they succeed or fail, with actor when known, time, action, outcome, and only the metadata needed to interpret the event; domain validation and concurrency failures remain technical logs and create no domain history. Permanent source context is limited to the client network prefix resolved through trusted-proxy rules, browser family and major version, OS family, and request correlation ID; raw headers, User-Agent, proxy chain, cookies, body, and URL query are excluded. An event never stores a password, token, secret, or planning content and remains separate from technical logs and domain revision histories. V1 retains it for the lifetime of the installation, permits no individual deletion, and removes it only when the installation database itself is destroyed. It is excluded from Workspace portability packages and cannot be imported, but the Workspace Owner may download the installation's audit history as a separate read-only JSON or CSV file; that audit export creates another Administrative Audit Event.
_Avoid_: Technical Log, Domain Revision, Notification, Planning History

**Restricted Audit Source Detail**:
The exact client IP associated with an Administrative Audit Event after trusted-proxy resolution. It is retained separately from the immutable event for no more than 90 days, requires fresh authentication and an explicit sensitive-data acknowledgement to access or export, and is then irreversibly deleted while the event's reduced network prefix remains. Its expiry does not mutate or delete the Administrative Audit Event.
_Avoid_: Permanent Audit Field, Proxy Chain, Planning History

**Operations Evidence Export**:
A read-only, versioned extract of installation-scoped Administrative Audit Events and Restore Test Records for external operational review. It includes a content checksum that can detect accidental corruption or changes to the exported artifact but does not prove that the installation database was never altered. By default it contains only the permanently retained client network prefix and the remaining permitted source context; the Workspace Owner may include exact client IPs whose Restricted Audit Source Details have not expired only after fresh authentication and an explicit sensitive-data warning, and that choice is itself recorded as an Administrative Audit Event. It remains separate from Workspace portability and cannot be imported as planning history.
_Avoid_: Workspace Export, Database Backup, Editable Audit Report

**Product**:
A distinct value-delivering offering for which a Product Owner maintains Product Goals, Initiatives, a Roadmap, and a Product Backlog.
_Avoid_: Project, Workspace

**Product Status**:
The lifecycle state of a Product: Active or Archived. An Archived Product is read-only, remains available for historical review and export, and may return to Active through an audited restoration.
_Avoid_: Product Deletion, Hidden Product, Initiative Status

**Product Archive Gate**:
The conditions required before an Active Product may become Archived: a recorded reason, every Product Goal Closed, every Initiative Completed or Cancelled, every Delivery Milestone Delivered or Cancelled, and every Product Backlog Item Done or Cancelled.
_Avoid_: Active Commitment Archive, Cascade Closure, Data Deletion

**Product Restoration**:
An audited transition that returns an Archived Product to Active, recorded with a reason, actor, and time. Existing Goals, Initiatives, Delivery Milestones, and Product Backlog Items remain terminal; renewed work requires new records or applicable successors.
_Avoid_: Cascade Reopen, Product Clone, Historical Rewrite

**Empty Product Deletion**:
The permanent removal of a Product that has never contained a Product Goal, Initiative, Delivery Milestone, Product Backlog Item, or Schedule Snapshot. Any Product with planning or delivery history must use Product Archive instead.
_Avoid_: Cascade Delete, Historical Product Deletion, Archive Substitute

**Product Owner**:
The person accountable for maximizing product value by ordering work and translating product goals into backlog decisions for a delivery team.
_Avoid_: Product Manager, Project Manager

**Attention Signal**:
A derived, non-persistent indication that prompts Product Owner review because a current condition requires attention. V1 aggregates Check-in Due, Delivery Milestone Overdue, Required By Overdue, and Risk Signal from all Active Products in one Workspace-wide in-app Attention Center, grouped by Product with filters but without cross-Product ranking; Archived Products are excluded. Within each Product, past commitment and deadline signals (`Overdue` and `Required By Overdue`) appear first with the oldest relevant date first, followed by `Risk Signal` and then `Check-in Due`; this is presentation order, not Backlog Order or automatic priority. Each signal also appears in its relevant context. It cannot be dismissed, snoozed, or marked read; it disappears only when its source condition is resolved, and it does not create a notification record or send email or push messages.
_Avoid_: Notification, Alert Record, Task, Lifecycle Status

**Product Goal**:
A desired future state of the product that provides purpose and direction for related Initiatives.
_Avoid_: Theme, Project Milestone

**Goal Status**:
The canonical lifecycle stage of a Product Goal: Draft, Active, or Closed.
_Avoid_: Initiative Status, Metric Result, Custom Goal Column

**Terminal Goal Status**:
Closed is an irreversible Goal Status that cannot become the Primary Product Goal of a new Initiative. Corrections must preserve an audit trail, and renewed measurement requires a Successor Product Goal.
_Avoid_: Reopened Goal, Reused Closed Goal, Silent Correction

**Successor Product Goal**:
A new Product Goal linked to a Closed Goal when the Product Owner continues or renews the outcome pursuit using a new Baseline, Target, and Target Date.
_Avoid_: Reopened Goal, Extended Closed Goal, Goal Version

**Goal Closure Result**:
The outcome recorded when a Product Goal becomes Closed: Achieved, Not Achieved, or Abandoned.
_Avoid_: Goal Status, Automatic Metric Result, Delivery Completion

**Not Achieved**:
A Goal Closure Result used after the Product Goal was pursued and evaluated but its Target was not met or a Guardrail Metric crossed an unacceptable boundary; closure requires supporting evidence and a learning summary.
_Avoid_: Abandoned, Delivery Failure, Automatic Target-date Result

**Abandoned**:
A Goal Closure Result used when the Product Owner stops pursuing or measuring a Product Goal before a conclusive outcome because strategy or context changed; closure requires a reason and learning summary.
_Avoid_: Not Achieved, Paused Goal, Goal Deletion

**Goal Activation Gate**:
The evidence required before a Draft Product Goal may become Active: a clear desired future state and one Primary Outcome Metric with its unit, measurement method, Baseline, Target, and Target Date. Guardrail Metrics are optional, and an Initiative is not required.
_Avoid_: Solution Gate, Name-only Goal, Initiative Requirement

**Goal Achievement Gate**:
The evidence required before an Active Product Goal may become Closed with the Achieved result: a Goal Check-in meeting the Primary Outcome Metric's Target, all Guardrail Metrics within their acceptable boundaries, and Product Owner confirmation with an achievement date, evidence, and closure summary. Target attainment does not close a Goal automatically.
_Avoid_: Automatic Achievement, Delivery Completion, Unsupported Closure

**Goal Closure Gate**:
The conditions required before an Active Product Goal may become Closed with any Goal Closure Result: every non-terminal Initiative using it as Primary Product Goal must first become Completed or Cancelled, or be reassigned to another Active Product Goal with a reason and audit trail. Supporting Product Goal links may remain as historical relationships.
_Avoid_: Active Primary Link, Cascade Initiative Cancellation, Historical Link Removal

**Outcome Metric**:
A quantitative measure used to determine whether the desired future state expressed by a Product Goal is being achieved, independently of work completion.
_Avoid_: Task Completion, Delivery Percentage

**Primary Outcome Metric**:
The single Outcome Metric whose movement from its Baseline to its Target determines whether a Product Goal succeeds.
_Avoid_: KPI List, Equal Metric

**Goal Measurement Definition**:
The Primary Outcome Metric, its Baseline, unit, and measurement method fixed when a Product Goal becomes Active. Their meaning cannot change while the Goal remains Active; a material change requires an Abandoned closure and Successor Product Goal, while corrections to erroneous data must preserve an audit trail.
_Avoid_: Moving Baseline, Metric Swap, Silent Data Correction

**Baseline**:
The observed value of a Primary Outcome Metric at the start of a Product Goal.
_Avoid_: Starting Estimate

**Target**:
The desired value of a Primary Outcome Metric that defines success for a Product Goal.
_Avoid_: Forecast, Stretch Wish

**Target Date**:
The date by which a Product Goal is expected to reach its Target.
_Avoid_: Initiative End Date, Release Date

**Goal Review Due**:
An automatically derived condition indicating that a Product Goal's Target Date has passed while its Goal Status remains Active. It requires a current Goal Check-in and a Product Owner decision to close the Goal or revise it; it is not a Goal Closure Result.
_Avoid_: Automatic Not Achieved, Goal Status, Silent Extension

**Goal Revision**:
An immutable record of a change to an Active Product Goal's Target or Target Date, including the previous and new value, reason, actor, and time. A current Goal Check-in must be recorded before the revision.
_Avoid_: Silent Goal Edit, Baseline Reset, Current Target Only

**Guardrail Metric**:
An Outcome Metric constrained to an acceptable boundary so that progress on a Primary Outcome Metric does not create an unacceptable side effect.
_Avoid_: Secondary Target, Nice-to-have KPI

**Guardrail Revision**:
An immutable, forward-effective record that adds a Guardrail Metric or tightens its acceptable boundary for an Active Product Goal, including the reason, actor, effective date, and time recorded. Relaxing a boundary or removing a Guardrail Metric requires an Abandoned closure and Successor Product Goal.
_Avoid_: Retroactive Guardrail, Relaxed Success Criterion, Silent Removal

**Goal Check-in Cadence**:
The Weekly or Monthly interval selected by the Product Owner when a Product Goal becomes Active, with Weekly as the default, for recording Goal Check-ins. Its schedule is anchored to the Goal Activation date: Weekly occurrences repeat every seven local calendar days, while Monthly occurrences repeat on the same local day of each month or that month's last day when the anchor day is unavailable. A late Goal Check-in advances to the first anchored occurrence still in the future and never shifts the underlying schedule.
_Avoid_: Real-time Feed, Unscheduled Check-in, Delivery Cadence

**Check-in Due**:
An automatically derived condition indicating that an Active Product Goal has passed an anchored Goal Check-in Cadence occurrence without a qualifying Goal Check-in. It begins immediately after the due occurrence's local day ends in the Workspace Time Zone, with no grace period. A late check-in clears the condition until the next anchored occurrence in the future; it does not change Goal Status, Goal Closure Result, or the cadence anchor.
_Avoid_: Automatic Closure, Missed Delivery, Goal Failure

**Goal Check-in**:
A dated observation of an Outcome Metric recorded by a Product Owner, together with optional evidence or context explaining the value.
_Avoid_: Delivery Update, Status Report

**Check-in Correction**:
An immutable record that supersedes an erroneous Goal Check-in with a corrected value, reason, actor, and time while preserving the original observation. Current trends use the corrected value and retain access to the correction history.
_Avoid_: Check-in Edit, Check-in Deletion, Hidden Correction

**Primary Product Goal**:
The single Product Goal that provides the principal justification for investing in an Initiative.
_Avoid_: Main Objective, Equal Goal

**Supporting Product Goal**:
An additional Product Goal that receives a material benefit from an Initiative but is not its principal justification.
_Avoid_: Secondary Initiative, Equal Goal

**Primary Product Goal Reassignment**:
An auditable change of a non-terminal Initiative's principal justification to another Active Product Goal in the same Product, recorded with the previous and new Goal, reason, actor, and time. The Product Owner must revalidate the expected product change, Forecast Window, and Planning Confidence; linked Product Backlog Items and delivery commitments do not change automatically.
_Avoid_: Silent Goal Swap, Cross-Product Goal, Automatic Delivery Revision

**Initiative**:
A substantial, coordinated product change chosen to advance one Primary Product Goal, optionally benefit Supporting Product Goals, and be represented on the Roadmap.
_Avoid_: Epic, Project, Roadmap Item

**Initiative Status**:
The canonical strategic lifecycle of an Initiative: Proposed, Planned, In Progress, Completed, or Cancelled. Completed and Cancelled are terminal; Completed means the intended delivery work finished, not that the associated Product Goal succeeded.
_Avoid_: Product Backlog Status, Milestone Status, Custom Roadmap Column

**Terminal Initiative Status**:
Completed and Cancelled are irreversible Initiative Statuses that cannot receive new Product Backlog Items or Delivery Milestones. Corrections must preserve an audit trail, and further work requires a Successor Initiative.
_Avoid_: Reopened Initiative, Post-completion Scope, Silent Correction

**Successor Initiative**:
A new Initiative linked to a terminal Initiative when continued or renewed product work needs its own planning lifecycle while preserving historical traceability.
_Avoid_: Reopened Initiative, Initiative Version, Added Terminal Scope

**Planning Gate**:
The evidence required before a Proposed Initiative may become Planned: a clear problem or opportunity, the expected product change, a Primary Product Goal, a Forecast Window, and Planning Confidence with its rationale and last-reviewed date. Product Backlog Items and delivery commitments are not required.
_Avoid_: Delivery Commitment, Ready Backlog Gate, Name-only Planning

**Initiative Start**:
The automatic transition of a Planned Initiative to In Progress when its first Strategic Product Backlog Item enters In Progress. The Initiative does not return to Planned automatically if that item's status later changes.
_Avoid_: Manual Start Status, Commitment-triggered Start, Automatic Reversion

**Initiative Completion Gate**:
The evidence required before an In Progress Initiative may become Completed: every linked Strategic Product Backlog Item is Done or Cancelled, every Delivery Milestone is Delivered or Cancelled, and the Product Owner records a completion summary. Reaching a Product Goal's Target is not required.
_Avoid_: Automatic Completion, Outcome Gate, Incomplete Child Work

**Initiative Cancellation Gate**:
The conditions required before a non-terminal Initiative may become Cancelled: a recorded cancellation reason, every Delivery Milestone already Delivered or Cancelled, and every unfinished Strategic Product Backlog Item either Cancelled or reassigned to another Initiative. The Initiative and its history are preserved.
_Avoid_: Cascade Cancellation, Active Child Work, Initiative Deletion

**Initiative On Hold**:
A non-terminal condition that the Product Owner may apply to a Planned or In Progress Initiative with a reason and review date. While active, no new Product Backlog Item may enter In Progress and no new Delivery Milestone may become Committed; existing Committed milestones must first be marked At Risk, revised, or cancelled.
_Avoid_: On Hold Status, Cancelled, Blocked

**Initiative Resume Gate**:
The evidence required before Initiative On Hold may be cleared: a resume reason and date plus a fresh review of the Forecast Window and Planning Confidence. Resuming does not clear At Risk or restore any revised or cancelled delivery commitment.
_Avoid_: Automatic Resume, Forecast Reuse, Commitment Restoration

**Delivery Progress**:
The extent to which an Initiative and its Product Backlog Items have been completed; it does not indicate whether a Product Goal has been achieved.
_Avoid_: Goal Progress, Outcome

**Roadmap**:
A time-oriented strategic planning view of Initiatives, their Forecast Windows, and the Product Goals they advance; it does not communicate delivery commitments and is not a separate container of work.
_Avoid_: Project Plan, Release Calendar

**Roadmap Visibility**:
The main Roadmap includes Planned and In Progress Initiatives, including those On Hold. Proposed Initiatives appear in a separate candidate view, while Completed and Cancelled Initiatives are hidden by default and available through historical filters.
_Avoid_: All-status Roadmap, Manual Publish Flag, Deleted History

**Roadmap Layout**:
The main Roadmap arranges Initiatives on a month- and quarter-oriented timeline using their Forecast Windows, with swimlanes grouped by Primary Product Goal. The Product Owner manually orders Initiatives that share the same lane and time window.
_Avoid_: Status Board, Milestone Schedule, Team Lane

**Concurrent Initiative Limit**:
An optional Product-level maximum set by the Product Owner for the number of Planned and In Progress Initiatives whose Forecast Windows overlap in a Roadmap period; Initiatives On Hold and Proposed, Completed, or Cancelled Initiatives are excluded. Resuming an Initiative includes it in the count again.
_Avoid_: Resource Capacity, Mandatory WIP Limit, Velocity

**Planning Load Warning**:
An advisory condition shown when overlapping Forecast Windows exceed the Product's Concurrent Initiative Limit. It does not block planning or move Initiatives automatically.
_Avoid_: Capacity Calculation, Automatic Reschedule, Planning Gate Failure

**Forecast Window**:
A month- or quarter-level interval in which an Initiative is currently expected to occur, expressed with planning confidence rather than as a delivery promise.
_Avoid_: Exact Date, Committed Date, Deadline

**Forecast Granularity**:
The Month or Quarter precision selected independently for each Initiative according to current uncertainty. The Roadmap nests months within quarters, and any granularity change is recorded in a Forecast Review.
_Avoid_: Product-wide Precision, Exact Dates, Hidden Precision Change

**Planning Confidence**:
The Product Owner's Low, Medium, or High judgment that an Initiative will remain within its Forecast Window, recorded with a rationale and last-reviewed date; it does not predict outcome success.
_Avoid_: Outcome Confidence, Automatic Readiness Score

**Forecast Review**:
An immutable internal record of each Product Owner review of an Initiative's Forecast Window and Planning Confidence, including the previous and reviewed values, rationale, actor, and time. The Roadmap shows the latest values while retaining review history.
_Avoid_: Schedule Revision, Delivery Commitment, Overwritten Forecast

**Delivery Schedule**:
A time-oriented view of committed product work intended for communication with users or stakeholders and derived from the same planning records as the Roadmap.
_Avoid_: Roadmap, Forecast View

**Delivery Schedule Layout**:
A chronological Delivery Milestone timeline and table ordered by Committed Date, showing each user-facing capability, Milestone Status, At Risk or Overdue condition, and relevant user-facing revision notes. It does not display engineering tasks, task durations, or dependency lines.
_Avoid_: Gantt Chart, Task Plan, Kanban Board

**Committed Date**:
A delivery date explicitly promised to users or stakeholders through the Delivery Schedule.
_Avoid_: Forecast Date, Target Date

**Delivery Milestone**:
A user-facing delivery commitment beneath an Initiative that states the meaningful capability to be delivered, its Committed Date, and its current delivery status.
_Avoid_: Product Backlog Item, Initiative, Internal Task

**Milestone Scope**:
The one or more Strategic Product Backlog Items from the same Initiative that collectively deliver a Delivery Milestone; a Product Backlog Item may belong to at most one active Milestone Scope.
_Avoid_: Free-text Scope, Cross-Initiative Scope, Duplicate Commitment

**Scoped Item Cancellation**:
A Product Backlog Item in a Committed Milestone Scope may become Cancelled only after it is removed through a Scope Revision or its Delivery Milestone is cancelled. An item in a Draft milestone must first be removed from its scope but does not require a published Scope Revision.
_Avoid_: Silent De-scope, Cancelled Item in Active Scope, Automatic Milestone Cancellation

**Milestone Status**:
The canonical commitment stage of a Delivery Milestone: Draft, Committed, Delivered, or Cancelled; only Committed milestones represent active delivery promises.
_Avoid_: Product Backlog Status, Custom Schedule Column

**Terminal Milestone Status**:
Delivered and Cancelled are irreversible Milestone Statuses. Further work or a renewed commitment requires a new Delivery Milestone linked to the original; corrections to terminal milestone details must preserve an audit trail.
_Avoid_: Reopened Milestone, Silent Correction, Status Reversal

**Commitment Gate**:
The evidence required before a Draft Delivery Milestone may become Committed: a user-facing capability, a Committed Date, a non-empty Milestone Scope, every scoped Product Backlog Item at least Ready, and no scoped item Blocked.
_Avoid_: Optional Warning, Approval Vote

**Delivery Gate**:
The evidence required before a Committed Delivery Milestone may become Delivered: every Product Backlog Item in its Milestone Scope is Done, and the Product Owner records an Actual Delivery Date and user-facing Delivery Evidence.
_Avoid_: Automatic Completion, Internal Done State

**Actual Delivery Date**:
The date on which the capability represented by a Delivery Milestone became available to its intended users.
_Avoid_: Committed Date, Product Backlog Item Completion Date

**Delivery Evidence**:
A user-facing label or note demonstrating that the capability represented by a Delivery Milestone became available to its intended users. It may contain an optional URL, but that URL becomes clickable in a Schedule Snapshot only after the Product Owner explicitly confirms that intended recipients can access it and that it contains no secret or access token; an unconfirmed URL is omitted from the Snapshot Artifact while the user-facing evidence text remains. The application validates URLs locally, permits only `https://`, rejects embedded credentials, and warns about query parameters, but never resolves or fetches the destination.
_Avoid_: Internal Completion Comment, Task Status

**Delivery Evidence Revision**:
An immutable record of a change to Delivery Evidence containing the previous and new evidence, reason, actor, and time. It never alters an existing Schedule Snapshot; corrected evidence appears only in a later superseding Snapshot and is disclosed by its Snapshot Change Summary.
_Avoid_: Snapshot Mutation, Link Redirect, Silent Evidence Edit

**Milestone Cancellation**:
The transition of a Delivery Milestone to Cancelled, ending any active commitment without claiming delivery while preserving its history and releasing its scoped Product Backlog Items for replanning. Cancelling a Draft requires an internal reason; cancelling a Committed milestone also requires a user-facing cancellation explanation.
_Avoid_: Deletion, Hidden Commitment, Delivered with Reduced Scope

**At Risk**:
A non-terminal condition explicitly applied by the Product Owner, with a user-facing explanation, when a Committed Delivery Milestone may miss its Committed Date; it preserves the Milestone Status.
_Avoid_: At Risk Status, Cancelled

**Risk Episode**:
The append-only history of a period during which a Delivery Milestone was At Risk, including the user-facing explanations, actors, and timestamps recorded when the Product Owner applied and cleared the condition. A transition to Delivered or Cancelled closes an active episode within the same action, using the Delivery Evidence or cancellation explanation as its closing context.
_Avoid_: Untracked Toggle, Unexplained Clearance, Current Risk Only

**Risk Signal**:
An internal prompt for Product Owner review when a Committed Delivery Milestone contains a Blocked Product Backlog Item or Broken Dependency. It does not apply At Risk automatically or appear as a user-facing commitment condition.
_Avoid_: Automatic At Risk, Overdue, User-facing Status

**Overdue**:
An automatically derived condition indicating that a Delivery Milestone's Committed Date has passed while it remains neither Delivered nor Cancelled.
_Avoid_: Overdue Status, At Risk

**Schedule Revision**:
An immutable record of a change to a Delivery Milestone's Committed Date, including the previous date, new date, reason, user-facing explanation, actor, and time of change.
_Avoid_: Silent Edit, Current Date Only

**Scope Revision**:
An immutable record of a change to a Committed Delivery Milestone's Milestone Scope, including the Product Backlog Items added or removed, reason, user-facing explanation, actor, and time of change. The revised scope must satisfy the Commitment Gate, and removed items are released for replanning.
_Avoid_: Silent Scope Edit, Scope Replacement, Hidden De-scope

**Schedule Snapshot**:
A timestamped, read-only export of a Delivery Schedule showing each user-facing capability, committed and actual dates, Milestone Status, At Risk or Overdue condition, user-facing risk, revision, or cancellation explanations, and Delivery Evidence that were current when the export was generated. Its explicit date horizon defaults from the current Workspace date through six months ahead but may be adjusted by the Product Owner; every unresolved Overdue milestone before the selected start remains included, and the exact horizon and filters are preserved in the snapshot. Draft milestones are excluded; Committed milestones are included when their Committed Date is inside the horizon or they are Overdue, while Delivered and Cancelled milestones are included when either their Committed Date or terminal-event date is inside the horizon. Every milestone that meets these rules is mandatory and cannot be omitted individually; an inappropriate commitment must be corrected at its source rather than hidden during export. Product Backlog Item names, internal reasons, and other backlog details are excluded. A Snapshot is retained immutably for the Product's lifetime, including after Product Archive; it cannot be edited or deleted, and a later Snapshot only supersedes it without removing the earlier record. Creation is blocked when the same Product already has a Snapshot with an identical horizon and user-facing content; the existing Snapshot must be reused or resent.
_Avoid_: Live Schedule, Editable Report

**Snapshot Reference**:
An immutable, human-readable identifier generated sequentially per Product in the form `S-YYYY-NNN`, using the Snapshot creation year in the Workspace Time Zone. It is displayed with Product name and generated timestamp, never reused, and complements rather than exposes the Snapshot's internal UUID.
_Avoid_: Filename, Timestamp-only Reference, User-defined Snapshot Name

**Snapshot Supersession**:
An explicit, optional, immutable link chosen when a new Schedule Snapshot replaces an older Snapshot from the same Product for the same communication series. A communication series is a linear chain: each Snapshot may have at most one predecessor and one successor, and branches, merges, and cycles are not permitted. If a selected Snapshot already has a successor, a replacement must continue from the current end of that chain. Supersession is blocked when the Snapshot Change Summary contains no user-visible change; the existing Snapshot must be reused or resent instead. The older Snapshot remains available and identifies its successor; no link means the new Snapshot is an independent publication rather than an inferred replacement based on time or horizon.
_Avoid_: Automatic Latest Snapshot, Snapshot Deletion, Horizon-based Replacement

**Snapshot Update Note**:
A required, concise, user-facing explanation recorded when a new Schedule Snapshot supersedes its predecessor, stating why the new publication was created and summarizing its material changes. It becomes an immutable part of the new Snapshot and is also visible from the predecessor's supersession lineage.
_Avoid_: Internal Revision Reason, Optional Comment, Snapshot Change Summary

**Snapshot Change Summary**:
An immutable, system-generated, user-safe comparison previewed before and included with a superseding Schedule Snapshot. It reports horizon changes, milestones entering or leaving the publication, and changes to user-facing dates, status, risk, explanations, or Delivery Evidence, while excluding Product Backlog Items, internal reasons, and raw technical field differences.
_Avoid_: Snapshot Update Note, Raw Field Diff, Internal Audit Diff

**Snapshot Artifact**:
A self-contained, immutable, static HTML representation captured when a Schedule Snapshot is created, including embedded print styles, render version, and a visible content-integrity fingerprint. It contains no JavaScript, forms, trackers, or externally loaded presentation assets; only confirmed recipient-safe Delivery Evidence may link to a network destination. Later viewing and browser Print to PDF use this stored artifact rather than current application UI or styles so that the published presentation does not change after application upgrades. The authenticated Workspace Owner may upload an artifact for local fingerprint verification without the uploaded file being retained; V1 does not claim sender authenticity or manage digital-signing keys. Browser-generated PDFs display the Snapshot Reference and source HTML fingerprint but remain derivatives that are not guaranteed to reproduce or verify byte-for-byte.
_Avoid_: Live Schedule Page, Regenerated Report, Stored PDF Binary

**Product Backlog**:
The ordered collection of product-level work for which the Product Owner is accountable.
_Avoid_: Task list, project plan

**Backlog Order**:
The explicit relative priority of Strategic and Operational Product Backlog Items in Backlog, Ready, or In Progress, decided by the Product Owner in one shared sequence; grouped and filtered views preserve this single canonical order. Done and Cancelled items are excluded, and scores or frameworks may inform but never determine the order automatically.
_Avoid_: Automatic Rank, Score Order

**Order Last Updated**:
The time at which the current Backlog Order most recently changed. V1 does not retain an event history for individual reorder actions.
_Avoid_: Reorder Audit Log, Item Updated Time, Priority Score Date

**Value**:
An optional Low, Medium, or High judgment of the expected benefit of a Product Backlog Item, recorded with a short rationale to inform the Product Owner's Backlog Order.
_Avoid_: Realized Outcome, Numeric Score, Automatic Priority

**Urgency**:
An optional Low, Medium, or High judgment of how time-sensitive a Product Backlog Item is, recorded with a short rationale to inform the Product Owner's Backlog Order.
_Avoid_: Committed Date, Automatic Deadline, Numeric Score

**Delivery Size**:
An optional relative judgment of the effort and complexity of a Product Backlog Item, expressed as XS, S, M, L, or XL. It is displayed with Value and Urgency but does not calculate Backlog Order, Forecast Windows, or Committed Dates.
_Avoid_: Job Size, Story Points, Duration, Automatic Forecast

**Traceability Coverage**:
The proportion of active Product Backlog Items whose reason for existence is explicitly linked to either an Initiative or an Operational Reason.
_Avoid_: Completion Rate, Delivery Progress

**Product Backlog Item**:
A product-level unit of desired change that can be proposed, ordered, prepared, delivered, and completed without representing the delivery team's technical subtasks. Every Product Backlog Item is either Strategic or Operational.
_Avoid_: Engineering Task, Subtask, Ticket

**Delivery Status**:
The canonical stage of a Product Backlog Item in the process-agnostic lifecycle Backlog, Ready, In Progress, Done, or Cancelled.
_Avoid_: Sprint Status, Custom Board Column

**Product Backlog Item Status Reversion**:
An explicit, reasoned transition from In Progress to Ready or from Ready to Backlog. An item must leave any Committed Milestone Scope through the applicable revision or cancellation rule before returning to Backlog; Done and Cancelled are terminal and cannot be reopened.
_Avoid_: Silent Reversion, Broken Commitment Gate, Terminal Reopen

**Product Backlog Item Cancellation**:
A reasoned terminal transition from Backlog, Ready, or In Progress to Cancelled that preserves the Product Backlog Item and its history. Applicable Milestone Scope rules must be satisfied first, and any dependent items receive a Broken Dependency rather than being cancelled automatically.
_Avoid_: Cascade Cancellation, Item Deletion, Silent Scope Removal

**Definition of Ready**:
The minimum evidence required before a Product Backlog Item may enter Ready: a clear problem or desired change, acceptance criteria, a Strategic or Operational reason, and all known dependencies declared.
_Avoid_: Optional Checklist, Engineering Approval Gate

**Product Backlog Item Start Gate**:
The conditions required before a Ready Product Backlog Item may enter In Progress: Blocked is not active, and a Strategic item belongs to a Planned or In Progress Initiative that is not On Hold. Unresolved dependencies remain advisory unless the Product Owner has applied Blocked, and a Delivery Milestone commitment is not required.
_Avoid_: Unresolved-dependency Block, Proposed Initiative Start, Commitment Requirement

**Definition of Done**:
The evidence required before an In Progress Product Backlog Item may become Done: its acceptance criteria are satisfied, all dependencies are resolved, Blocked is cleared, completion evidence or a summary is recorded, and the Product Owner confirms completion. A Done item does not make its Delivery Milestone Delivered automatically.
_Avoid_: Task Completion Rollup, Automatic Milestone Delivery, Status-only Completion

**Product Backlog Item Dependency**:
A directed prerequisite relationship in which one Product Backlog Item depends on another Product Backlog Item within the same Product.
_Avoid_: Cross-Product Link, Free-text Dependency, Engineering Task Dependency

**Product Backlog Item Dependency Resolution**:
A Product Backlog Item Dependency becomes resolved automatically when its prerequisite item reaches Done; the relationship remains recorded for traceability. Resolution clears its Dependency Warning but does not remove Blocked from the dependent item.
_Avoid_: In-progress Resolution, Manual Confirmation, Dependency Deletion

**Broken Dependency**:
An unresolved Product Backlog Item Dependency whose prerequisite item has been Cancelled. The dependent item cannot reach Done until the Product Owner removes or replaces the dependency with a recorded reason; this condition does not automatically apply Blocked or cancel dependent items.
_Avoid_: Resolved Dependency, Automatic Cascade Cancellation, Silent Link Removal

**Dependency Cycle**:
An invalid chain of Product Backlog Item Dependencies that returns to its starting item. The relationship that would create the cycle is rejected and the full cycle is identified for correction.
_Avoid_: Circular Plan, Warning-only Cycle, Automatic Block

**External Dependency**:
A prerequisite outside the Product's structured backlog, recorded with a name, responsible owner, and expected resolution date.
_Avoid_: Cross-Product Backlog Link, Unowned Note, Internal Product Backlog Item

**External Dependency Resolution**:
The Product Owner's confirmation that an External Dependency has been resolved, recorded with its Actual Resolution Date and optional evidence or context. It clears the associated Dependency Warning but does not remove Blocked from a Product Backlog Item.
_Avoid_: Expected-date Completion, Automatic Unblock, Dependency Deletion

**Dependency Warning**:
A signal that a declared Product Backlog Item Dependency or External Dependency remains unresolved; it does not automatically change the Product Backlog Item's Delivery Status or apply Blocked.
_Avoid_: Automatic Block, Delivery Status, Commitment Failure

**Blocked**:
A current condition explicitly recorded by the Product Owner, with a reason and Blocked Since timestamp, when a Product Backlog Item cannot currently progress; it preserves the item's existing Delivery Status. The Product Owner clears it explicitly, and V1 does not retain append-only blocker episodes.
_Avoid_: Blocked Status, Paused Column

**Blocked Since**:
The time at which the current Blocked condition was applied to a Product Backlog Item; it is absent after the condition is cleared and resets if the item is blocked again.
_Avoid_: Original Block Date, Blocker History, Delivery Start Date

**Strategic Product Backlog Item**:
A Product Backlog Item whose reason for existence is an Initiative that advances a Product Goal.
_Avoid_: Roadmap Item

**Strategic Product Backlog Item Reassignment**:
An auditable change of a Strategic Product Backlog Item's Initiative within the same Product, recorded with its previous and new Initiative, reason, actor, and time. Backlog or Ready items may move to any non-terminal Initiative; an In Progress item may move only to Planned or In Progress and starts a Planned destination automatically; Done and Cancelled items cannot move. An item in an active Milestone Scope must first be removed through the applicable scope revision or milestone cancellation rule.
_Avoid_: Cross-Product Move, Silent Relink, Active Scope Bypass

**Operational Product Backlog Item**:
A Product Backlog Item justified by an Operational Reason rather than an Initiative.
_Avoid_: Other Item, BAU Item

**Required By Date**:
An optional externally imposed deadline on an Operational Product Backlog Item, recorded with its reason or authoritative source. Strategic Product Backlog Items do not have their own due dates and receive delivery timing only through a Delivery Milestone.
_Avoid_: Committed Date, Strategic Due Date, Arbitrary Deadline

**Required By Overdue**:
An automatically derived condition indicating that an Operational Product Backlog Item's Required By Date has passed while its Delivery Status remains neither Done nor Cancelled. It prompts Product Owner review but does not change Delivery Status, Blocked, or Backlog Order.
_Avoid_: Overdue Status, Automatic Block, Automatic Priority

**Required By Revision**:
An immutable record of a change to an Operational Product Backlog Item's Required By Date, including the previous and new dates, reason or authoritative source, actor, and time.
_Avoid_: Silent Deadline Edit, Automatic Extension, Current Date Only

**Operational Reason**:
A classified non-strategic obligation, such as an Incident, Compliance requirement, or Maintenance need, that explains why an Operational Product Backlog Item exists.
_Avoid_: Other, Miscellaneous, Initiative

**Engineering Task**:
Implementation-level work performed by the delivery team beneath a Product Backlog Item.
_Avoid_: Product Backlog Item
