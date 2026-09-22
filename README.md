# navia-launcher-releases

Hosts release builds and the update manifest (`latest.json`) for **Navia FYT
Launcher**, so the car's head unit can update itself over Wi-Fi/SIM instead of
needing a USB pendrive trip. Consumed by `AppUpdateManager.kt` in the main
[Stacja_android_auto](https://github.com/apm-medyka) project.

Mirrors the same hosting pattern as
[navia-offline-maps](https://github.com/apm-medyka/navia-offline-maps): a
manifest committed at a fixed path on `master`, referencing this repo's
GitHub Releases assets via the `releases/latest/download/...` URL, which
always resolves to whatever the most recent release tag published.

- Manifest: `latest.json` (raw URL is the launcher's default, hardcoded in
  `AppUpdateManager.DEFAULT_MANIFEST_URL`)
- APK: attached to each GitHub Release as `navia-launcher-<versionName>.apk`

## Publishing a new build

The launcher currently ships **debug-signed** builds only (no release
signing key has been set up - this is a solo hobby project, not something
distributed beyond one car). A self-update must be signed with the *same*
key as what's already installed, or Android refuses the install as a
signature mismatch - so updates are debug APKs too, consistent with what's
already flashed to the tablet today.

From the main repo:

```powershell
tools\app-update\publish-release.ps1 -VersionName "1.1.0" -VersionCode 2 -ReleaseNotes "Opis zmian dla kierowcy"
```

This bumps `versionCode`/`versionName` in `launcher/build.gradle.kts`,
builds `assembleDebug`, computes its SHA-256, creates a GitHub Release here
tagged `vX.Y.Z` with the APK attached, updates `latest.json` in this repo,
and pushes it - see `tools/app-update/README.md` for the full manual
fallback if the script can't run (e.g. `gh` not authenticated on that
machine).
