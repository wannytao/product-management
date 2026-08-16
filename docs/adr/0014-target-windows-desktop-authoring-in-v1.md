# Target Windows desktop authoring in V1

V1 will guarantee the full planning and administration interface on the current and immediately previous stable major versions of Microsoft Edge and Google Chrome on Windows at viewport widths of at least 1280 CSS pixels. Full tablet and mobile authoring are deliberately outside the six-week validation scope because roadmap, backlog-ordering, and schedule workflows are information-dense and would materially expand interaction and layout testing.

Immutable Snapshot Artifacts remain static documents that must be readable and printable on smaller modern-browser screens; this does not imply support for the main application workflow on those devices.

The supported application and Snapshot Artifact surfaces will target WCAG 2.2 Level AA, including keyboard operation, visible focus, semantic names, sufficient contrast, identifiable validation errors, and accessible authentication. V1 will verify this target across its supported workflows but will not claim independent certification or broader device/browser conformance than this ADR defines.

Accessibility is a release gate: supported pages and components will receive automated checks, while critical workflows will also receive manual keyboard and screen-reader testing before release. A known Level A or AA defect in a supported V1 surface blocks release rather than being deferred as ordinary backlog work.

Manual release testing will use Windows Narrator with Microsoft Edge as the primary screen-reader path across all critical workflows. NVDA with Google Chrome will provide a secondary smoke test covering authentication, navigation, forms, accessible Backlog reordering, and Schedule Snapshot reading.

Status, Planning Confidence, At Risk, and Overdue will always be communicated with visible text plus a distinguishable icon or shape; color is supplementary and never the sole carrier of meaning. The same meaning must remain available to assistive technology and in grayscale printed Snapshot output.

The strategic Roadmap timeline will have a synchronized semantic table or list using the same records, filters, and ordering. It will expose Initiative, Product Goal, Forecast Window, status, and Planning Confidence through full keyboard operation; it is an equivalent supported view rather than hidden screen-reader-only content.

Failed form or lifecycle-gate validation will preserve entered values, move focus to an error summary linked to every invalid field, and also render an inline error programmatically associated with each field. Gate errors will state the unmet condition and corrective action rather than rely on disappearing toasts or unexplained disabled submission controls.

Canonical Backlog Order will support both pointer drag-and-drop and keyboard-accessible move commands. Commands will allow moving an item to the top or bottom or before or after a searched Product Backlog Item, and assistive technology will receive confirmation of the resulting position; drag interaction will never be the only way to reorder work.
