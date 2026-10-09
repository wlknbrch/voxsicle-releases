# Voxsicle Releases

This repository distributes official release assets for **Voxsicle**, a local-first macOS studio for synthetic voice production.

Voxsicle combines a persistent Voice Library, local voice generation, long-form rendering, Dialogue Studio, pronunciation controls, production audio, and export tools in one desktop workspace. Voice references, generated audio, scripts, projects, and pronunciation data stay on your Mac.

> This is a release-assets repository. It does not contain the Voxsicle source code.

## Current platform

The current experimental release supports **macOS on Apple Silicon** only (M-series Macs).

Support for Intel Macs, Windows, and Linux is planned for a later release.

## Install Voxsicle

1. Open the latest release and download the `Voxsicle-1.4.3-arm64.dmg` asset.
2. Open the downloaded DMG.
3. Drag **Voxsicle** to the **Applications** folder.
4. Open the app from Applications.

The release app includes its local backend. Normal use does not require a separate Python or pnpm installation. The optional local-signing workaround below may prompt macOS to install its free Command Line Tools.

### macOS security notice and local signing

These releases are currently **not notarized by Apple and do not have a Developer ID distribution signature**. macOS Gatekeeper may therefore block the first launch with a message such as “Apple cannot check this app for malicious software”, “the developer cannot be verified”, or “the app is damaged and can't be opened”. The dialog may offer **Move to Trash** without an **Open** option.

#### 1. Try the macOS approval option first

1. Download the DMG only from [this repository's official releases](https://github.com/slinkysloperatjohn-source/voxsicle-releases/releases). If a checksum is provided, verify it as described below.
2. Copy **Voxsicle.app** to **Applications** and eject the DMG. Work with the installed copy, not the app inside the disk image.
3. Try opening the installed app once. Dismiss the warning without moving the app to Trash.
4. Open **System Settings → Privacy & Security**, scroll to **Security**, and select **Open Anyway** for Voxsicle, if available. In German macOS this is **Systemeinstellungen → Datenschutz & Sicherheit → Dennoch öffnen**.
5. Confirm **Open** and authenticate if prompted.

Depending on the macOS version, Control-clicking the app and choosing **Open** may also work. If no approval option is available, or the installed copy remains blocked, continue below.

#### 2. Locally ad-hoc sign the installed app

Close Voxsicle first, then open **Terminal** (Applications → Utilities). Copy the entire block below and press Return:

```bash
(
  APP="/Applications/Voxsicle.app"

  if [ ! -d "$APP" ]; then
    printf 'App not found: %s\nCopy the app to Applications first.\n' "$APP" >&2
    exit 1
  fi

  /usr/bin/xattr -dr com.apple.quarantine "$APP" &&
  /usr/bin/codesign --force --deep --sign - --preserve-metadata=identifier,entitlements,flags "$APP" &&
  /usr/bin/codesign --verify --deep --strict --verbose=2 "$APP" &&
  /usr/bin/open "$APP"
)
```

This removes the download quarantine attribute **only from this app bundle**, replaces its local code signatures with ad-hoc signatures, verifies them, and opens the app only if those steps succeed. The existing identifier, entitlements, and signing flags are preserved when available. The `-` after `--sign` means **ad-hoc signing**: no Apple Developer subscription, Apple account, or signing certificate is required.

**Only do this for a download you trust.** Removing quarantine bypasses the normal downloaded-app Gatekeeper check for this copy; local signing does not notarize the app, verify its publisher, or prove that it is safe. Do not use these steps to override an explicit “will damage your computer” malware warning. Do not disable Gatekeeper globally.

#### Troubleshooting

- **Different installation location:** Change the `APP="..."` line to the actual path of the installed `.app`. Keep the quotation marks, especially if the path contains spaces.
- **Permission denied / Operation not permitted:** Make sure the app is closed and you are modifying the copy in Applications, not the read-only DMG. If the copy belongs to an administrator, an administrator can run the same `xattr` and `codesign --force ...` commands with `sudo` before each command, using the same quoted app path. Terminal does not display characters while an administrator password is entered.
- **Developer tools requested:** If macOS requests Command Line Tools when running `codesign`, install the free tools with `xcode-select --install`, finish the installation, and rerun the block. A paid developer membership is not required.
- **Signing or verification fails:** Stop and retain the exact Terminal error. Download a fresh copy from the official release and try again. If it still fails, [open an issue](https://github.com/slinkysloperatjohn-source/voxsicle-releases/issues) with your macOS version, app version, and error. Recursive `--deep` signing is a local repair fallback; some bundled components may require a corrected release. A valid local signature does not guarantee that every runtime component will start.
- **After an update or reinstall:** The replacement app may need these steps again. They affect the installed app bundle, not your projects, models, or files in Application Support.

See [Apple's instructions for opening an unnotarized app](https://support.apple.com/en-us/102445).

## Local-first by design

Voxsicle does not use cloud inference, accounts, or telemetry. The desktop app keeps voice recordings, generated audio, scripts, projects, and pronunciation data locally on the device.

Models are never downloaded silently. Open **Models** in Voxsicle and choose **Install model** only for the local engines you want to use. Depending on the selected engine, the initial download can be several gigabytes and may take some time.

## First steps

1. Open **Voices** and create a Voice Profile.
2. Import recordings that you are allowed to use as references.
3. Review the references and optionally choose one as the preferred sample.
4. Open **Models** and explicitly install a local engine.
5. Open **Speak**, select the Voice Profile and an installed engine, then create a render.

Use only voices, recordings, and scripts that you have permission to process. Results depend on the quality of the source material and the selected local model.

## Included asset

| Asset | Purpose |
| --- | --- |
| `Voxsicle-1.4.3-arm64.dmg` | The desktop application installer for Apple Silicon Macs. |

If a SHA-256 checksum file is attached to a release, verify the downloaded asset before opening it:

```bash
shasum -a 256 "Voxsicle-1.4.3-arm64.dmg"
```

## Your local data

On macOS, Voxsicle stores its managed data in:

```text
~/Library/Application Support/Voxsicle/
```

That location can contain Voice Profiles, reference audio, generated takes, exports, model files, backups, and settings. Removing the app from Applications does not automatically remove those files. Use the application's backup and library tools before deleting local data.

## Experimental release notice

Voxsicle is free to use and still evolving. Keep backups of important recordings and exports, and expect first-release limitations while the product and platform support continue to develop.
