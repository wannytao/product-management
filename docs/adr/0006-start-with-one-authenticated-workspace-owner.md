# Start with one authenticated Workspace Owner

The self-hosted V1 will require local authentication and support one Workspace Owner, with no public registration, invitations, or role-based access controls. This protects sensitive planning data even when a deployment is accidentally network-accessible and establishes an authenticated ownership boundary for future collaboration without paying the immediate complexity cost of multi-user authorization.

The interactive host `setup.ps1` will expose a one-time setup token only through the operator console so that the Workspace Owner can create the local password; the setup path will be disabled permanently after the owner is created. Account recovery will require a local CLI command on the host, and a password reset will revoke every existing authenticated session. V1 will not depend on email or SMTP for account setup or recovery.

An authenticated session will expire after 12 hours of inactivity and will have an absolute lifetime of seven days even while actively used. Explicit logout will revoke the current session immediately, and the account-recovery procedure will continue to revoke all sessions.

Failed login protection will use capped exponential delays associated with the Workspace Owner account plus a secondary request ceiling per source IP. Delays and IP mitigation remain temporary, no permanent account lockout or CAPTCHA is introduced, and local CLI recovery on the host remains available if legitimate access is disrupted. Successful and failed security-sensitive attempts continue to create Administrative Audit Events without storing submitted credentials.

The throttling policy uses a rolling 15-minute observation window. Exponential account delay begins on the fifth failed attempt at one second and grows to a maximum of 15 minutes; a source IP exceeding 30 attempts in 15 minutes is paused for 15 minutes. A successful login or 30 minutes without another failure clears the account counter.

The single-factor owner password will be 15 to 128 Unicode characters, normalized with NFC, and will support spaces, paste, autofill, and passphrases. Setup and password changes will compare the complete normalized value with a local common-or-compromised-password blocklist; V1 will impose no character-class composition rules and no periodic rotation, while requiring a change when compromise is suspected or confirmed.

The blocklist will be a versioned, tested application-release asset augmented with context-specific values such as application and owner identifiers. It will be updated through controlled application upgrades, not by an operator-managed file or live breach-service request, preserving the V1 no-outbound-service boundary.

V1 will use password-only authentication and will not implement a second factor. Optional TOTP with one-time recovery codes and local CLI recovery is the preferred later direction if remote-use evidence justifies it; SMS and email OTP are not planned. This deferral is acceptable only within the private-by-default deployment boundary, not as a basis for exposing the application directly to the public Internet.

V1 will preserve an authenticator-neutral boundary between credential verification and session issuance, but it will not reserve unused TOTP columns, recovery-code tables, secrets, feature flags, or hidden UI. A later MFA release will introduce its own reviewed schema migration and security tests rather than activate dormant authentication code.

Passwords will be salted and hashed with Argon2id. Setup and controlled upgrades will calibrate parameters to approximately 250–500 milliseconds of verification work on the deployment host without falling below the current OWASP minimum; the encoded hash will retain its algorithm and parameters, and a successful login will rehash with a stronger current policy when required.

Setup will also generate an installation-specific password pepper stored in protected application configuration outside PostgreSQL, source control, database backups, and Workspace exports. Loss or replacement of the pepper makes the existing password verifier unusable and therefore requires a local CLI password reset that revokes every active session; V1 will not bind the pepper to one Windows machine or depend on an external secrets vault.

Every database restore will revoke all session records before the restored installation accepts requests, even when the original pepper remains available. Replacement-host recovery will generate a new pepper and require the local CLI recovery command to set a new Workspace Owner password; it will not preserve access by escrowing the old pepper with a backup or Recovery Kit.

Authentication will issue a high-entropy opaque session token while retaining session state, expiry, and revocation in PostgreSQL. The token will rotate after login and security-sensitive account changes; V1 will not place session state in signed cookies, use JWT access or refresh tokens, or rely on process memory for authoritative sessions.

Cookie transport has two explicit deployment profiles. Loopback-only HTTP may use a host-only `HttpOnly; SameSite=Strict; Path=/` session cookie without `Secure`; any non-loopback access requires HTTPS through an explicitly configured trusted proxy and a `__Host-` session cookie with `Secure`, `HttpOnly`, `SameSite=Strict`, and `Path=/`. The application will reject non-loopback HTTP and will not trust forwarded transport headers from an unconfigured proxy.

Every state-changing HTTP request will require a server-issued synchronizer token bound to the authenticated session plus valid same-origin request context. `Origin` or, where appropriate, `Referer` will be checked, `GET`, `HEAD`, and `OPTIONS` will never change state, and `SameSite=Strict` remains defense in depth rather than the sole CSRF control.

Password change, Workspace import or export, Operations Evidence export, and loopback-only Emergency Trust Reset require password re-authentication when the session's latest authentication proof is older than 15 minutes. Host-level security and network configuration remains outside the web application and is changed through operator tooling. Successful step-up authentication refreshes authentication age and rotates the opaque session token; ordinary product-domain commands continue to rely on the active session and their dedicated review or concurrency controls.
