# Separate the Roadmap from the Delivery Schedule

The application will expose a strategic Roadmap and a committed Delivery Schedule as distinct views over the same planning records. The Roadmap explains which Initiatives advance Product Goals using month- or quarter-level Forecast Windows with Planning Confidence, without implying a delivery promise. Planning Confidence is selected by the Product Owner as Low, Medium, or High and must include a rationale and last-reviewed date; it is not calculated from backlog readiness and does not predict outcome success. The Delivery Schedule communicates committed dates to users or stakeholders; sharing source records avoids duplicated status while preserving the different meaning and audience of each view.

Delivery Schedules and immutable Schedule Snapshots will provide print-optimized HTML for browser Print to PDF using A4 landscape as the default page format. Printed output will repeat table headers, include page numbers and the generated timestamp, omit interactive controls, and preserve readable column boundaries across page breaks.

Each Schedule Snapshot will retain a self-contained immutable HTML artifact with embedded print styles, a render version, and a content-integrity hash captured at creation. Later viewing and printing will use that stored artifact rather than regenerate it with current application components or CSS; V1 will not add a server-side PDF renderer.

The stored artifact will be an inert document: it will contain no JavaScript, forms, trackers, or externally loaded presentation assets. Only Delivery Evidence links explicitly confirmed as recipient-safe may navigate to a network destination.

V1 stakeholder sharing will be file-based only: the authenticated Workspace Owner may download the immutable HTML artifact or print it to PDF and distribute that file through an external channel. The application will not create anonymous or signed public links, external-recipient accounts, recipient tracking, or outbound email delivery.
