# Cd Study downloads

Desktop installers and update feeds for [Cd Study](https://celedus.app/download).
The application source is kept in a separate private repository. Only this
workflow, build logs, packaged application artifacts, and releases are public.

## Build and publish

Open **Actions → Desktop release → Run workflow**.

- **nightly** builds the selected private source ref (default `main`).
- **stable** requires an explicit version such as `0.1.7`.
- Leave **publish** unchecked for the initial build check. Downloads remain
  temporary workflow artifacts and are removed after one day.
- Check **publish** to attach installers, updater manifests and SHA-256 checksums
  to a GitHub release. Nightlies are prereleases; stable releases become latest.

Tag-triggered stable releases use matching tags in both repositories: tag the
private source first, then push the same `vX.Y.Z` tag here. The workflow checks
out that private tag and publishes the matching version.

Nightlies run daily at 06:17 UTC once the repository variable `NIGHTLY_ENABLED`
is `true`. They skip commits already present in a published nightly. Leave this
variable unset until the first release has been verified.

## Credentials

`SOURCE_DEPLOY_KEY` is an Actions secret containing a read-only deploy key for
the private source repository. It cannot push source changes. No personal token
is needed. The workflow does not accept pull-request triggers.

Public repository variables used by desktop sign-in:

- `T3CODE_SUPABASE_URL`
- `T3CODE_SUPABASE_PUBLISHABLE_KEY` (publishable or legacy anon key only)

macOS signing uses Actions secrets `CSC_LINK` (base64 .p12), `CSC_KEY_PASSWORD`,
`APPLE_API_KEY` (raw .p8), `APPLE_API_KEY_ID`, `APPLE_API_ISSUER`, and
`MACOS_PROVISIONING_PROFILE` (base64 profile), plus the variable `APPLE_TEAM_ID`.
The profile must match `app.celedus.study`. Keep private keys out of this repo.

Without Apple credentials, builds are unsigned and macOS will block normal
installation. Configure signing before publishing downloads for end users.
Windows builds currently have no code-signing certificate and may show SmartScreen.

Build targets: macOS Apple Silicon and Intel, Linux x64 (AppImage), and Windows
x64 (NSIS, with the matching Linux runtime bundled for WSL).
