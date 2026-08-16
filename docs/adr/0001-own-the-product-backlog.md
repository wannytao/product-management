# Own the Product Backlog without becoming a full engineering tracker

The application will be the system of record for both the product roadmap and the Product Backlog so that product intent and delivered work remain traceable in one place. It will own Product Backlog Items through completion, but deliberately exclude engineering subtasks and the deeper workflow expected from a full Jira or Linear replacement; this favors a cohesive Product Owner workflow while containing product scope.

Canonical Backlog Order changes will use optimistic concurrency. A reorder command based on an outdated order version will be rejected and the latest order shown for an explicit retry; the application will neither apply last-write-wins nor silently merge relative-move intent from stale tabs.
