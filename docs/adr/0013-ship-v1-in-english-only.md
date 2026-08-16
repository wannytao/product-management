# Ship the V1 interface and generated outputs in English only

V1 will use English for the internal interface, validation messages, help text, and all application-generated labels in Delivery Schedules and Schedule Snapshots. User-entered planning content will support Unicode and may use Thai or any other language, but the application will not translate that content or generate Thai-labelled outputs in V1.

This deliberately protects the six-week validation window from full bilingual copy, layout, and QA scope. If Thai-labelled stakeholder communication becomes necessary during validation, it will be treated as evidence for a later localization decision rather than added implicitly to V1.

All application-owned English labels, validation messages, help text, and generated-output text will be defined in a centralized typed message catalog rather than embedded throughout components or commands. V1 will not add language switching, locale negotiation, or runtime translation loading; the catalog is an extension point, not a hidden multilingual feature.

Human-facing dates will use an unambiguous English `d MMM yyyy` form such as `15 Aug 2026`; times will use a 24-hour `HH:mm` form and the Gregorian calendar. Event timestamps will display the Workspace Time Zone where temporal context matters. These presentation rules do not change UTC timestamp storage, date-only storage, or machine-readable ISO representations.
