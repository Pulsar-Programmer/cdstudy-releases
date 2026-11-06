# Cd Study downloads

Desktop installers and update feeds for [Cd Study](https://celedus.app/download),
the Celedus app for working with coding agents from desktop, web, and mobile.
Download published installers from [Releases](https://github.com/Pulsar-Programmer/cdstudy-releases/releases).

The application source lives in the separate private `Pulsar-Programmer/celedus`
repository. This public repository contains the desktop release workflow,
build logs, packaged artifacts, and releases. It does not build the hosted web
app or mobile clients.

## Build and publish

Open [Actions → Desktop release → Run workflow](https://github.com/Pulsar-Programmer/cdstudy-releases/actions/workflows/release.yml).

- **source_ref** selects a branch, tag, or commit in the private source repository
  (default `main`). Every build in the run uses the same resolved source commit.
- **nightly** derives the next patch version from the source desktop package,
  with a `-nightly.YYYYMMDD.RUN_NUMBER` suffix.
- **stable** requires an explicit version such as `0.1.7`.
- Leave **publish** unchecked to check a build. Downloads remain temporary
  workflow artifacts and are removed after one day.
- Check **publish** to attach installers, updater manifests, the Linux x64 CLI
  runtime archive, and `SHA256SUMS` to a GitHub release after all builds pass.
  Nightlies are prereleases; stable releases become latest. Release notes record
  the private source commit.

Tag-triggered stable releases use matching tags in both repositories: tag the
private source first, then push the same `vX.Y.Z` tag here. The workflow checks
out that private tag and publishes the matching version.

Nightlies run daily at 06:17 UTC when the repository variable `NIGHTLY_ENABLED`
is `true`. They skip commits already present in a published nightly. Leave this
variable unset until the first release has been verified.

These instructions describe this repository's `Desktop release` workflow. The
private source repository also has its own release workflow; its channels,
schedule, build targets, and publication steps are separate.

## Configuration and the Cd Study refactor

The current source checkout is being refactored from `T3CODE_*` environment
variables to `CDSTUDY_*`. At the time of this README update, that rename is still
uncommitted: GitHub's private source `main` and this public release workflow
still use the old names. Use the names accepted by the selected **source_ref**.
Before building a refactored source ref, align the workflow exports and repository
variables with the following names:

| Existing workflow name | Refactored source name | Purpose |
| --- | --- | --- |
| `T3CODE_SUPABASE_URL` | `CDSTUDY_SUPABASE_URL` | Supabase project URL for Celedus sign-in |
| `T3CODE_SUPABASE_PUBLISHABLE_KEY` | `CDSTUDY_SUPABASE_PUBLISHABLE_KEY` | Publishable or legacy anon key only |
| `T3CODE_CELEDUS_SITE_URL` | `CDSTUDY_CELEDUS_SITE_URL` | Celedus site origin, `https://celedus.app` |
| `T3CODE_DESKTOP_UPDATE_REPOSITORY` | `CDSTUDY_DESKTOP_UPDATE_REPOSITORY` | Desktop update repository, `Pulsar-Programmer/cdstudy-releases` |
| `T3CODE_TELEMETRY_ENABLED` | `CDSTUDY_TELEMETRY_ENABLED` | Set to `false` by this workflow |
| `T3CODE_APPLE_TEAM_ID` | `CDSTUDY_APPLE_TEAM_ID` | Apple team ID passed to the packager |
| `T3CODE_MACOS_PROVISIONING_PROFILE` | `CDSTUDY_MACOS_PROVISIONING_PROFILE` | Decoded provisioning profile path passed to the packager |

The workflow currently reads the two Supabase values from repository variables.
Renaming those variables alone does not update the workflow's `vars.*` references
or its exported environment names. The Apple repository variable remains
`APPLE_TEAM_ID`, and the profile secret remains `MACOS_PROVISIONING_PROFILE`;
only their exports to the source packager change.

The refactored source uses `CDSTUDY_HOME` with a default data directory of
`~/.cdstudy`, and the desktop application ID is `app.celedus.study`. The CLI
command remains `t3`; internal package names such as `@t3tools/*` also remain.
The variable rename does not imply that every internal `t3` identifier changed.

## Credentials and signing

`SOURCE_DEPLOY_KEY` is an Actions secret containing a read-only deploy key for
the private source repository. It cannot push source changes. No personal token
is needed. The workflow does not accept pull-request triggers.

macOS signing uses Actions secrets `CSC_LINK` (base64 .p12), `CSC_KEY_PASSWORD`,
`APPLE_API_KEY` (raw .p8), `APPLE_API_KEY_ID`, `APPLE_API_ISSUER`, and
`MACOS_PROVISIONING_PROFILE` (base64 profile), plus the variable `APPLE_TEAM_ID`.
The profile must match `app.celedus.study`. Keep private keys out of this repo.

Without Apple credentials, builds are unsigned and macOS will block normal
installation. Configure signing before publishing downloads for end users.
Windows builds currently have no code-signing certificate and may show SmartScreen.

## Build targets

- macOS Apple Silicon (`arm64`) and Intel (`x64`): DMG installers and ZIP update assets.
- Linux `x64`: AppImage and a self-contained CLI runtime archive.
- Windows `x64`: NSIS installer with the matching Linux `x64` runtime bundled for WSL.

The macOS updater manifests are merged so one feed covers both architectures.
Linux ARM64, Windows ARM64, and mobile builds are not targets of this public
workflow. The desktop update repository override does not change the CLI's
release download source; that is configured separately in the application source.
