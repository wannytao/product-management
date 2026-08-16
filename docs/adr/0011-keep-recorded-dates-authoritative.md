# Keep recorded dates authoritative and make future holiday adjustment advisory

V1 will evaluate date-only values exactly as recorded in the Workspace Time Zone, including dates that fall on weekends or holidays, and will not include a business or holiday calendar. Date-only storage will not inherit the host or browser time zone, preserving a clean extension point for later calendar-aware behavior.

A future Holiday Calendar may suggest the next working date while a Product Owner enters or edits a date, but the Product Owner must confirm the proposed value before it is saved. The application will not silently shift an already recorded date or retroactively alter immutable history or published snapshots. Fully automatic date rollover was considered but rejected because it could change an explicit target, deadline, or commitment without accountable product judgment.
