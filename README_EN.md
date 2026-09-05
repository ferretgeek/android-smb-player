# LAN Video Player · LanPlay

[中文](./README.md) · English

Play videos from a computer or NAS shared folder on your Android phone or tablet, with subtitles and playback resume.

**Requirements:** Android 8+ and access to an SMB share. Source code is available; build the APK yourself.

[Build and install](#building-it-yourself) · [Screenshots](#interface) · [Usage and troubleshooting](./docs/OPERATIONS.md) · [Desktop poster tool](./lanplay-scraper/README.md)

## What it does

- **Browse shared videos:** LAN scanning, share discovery, guest or account access, search, and sorting.
- **Playback and subtitles:** Media3 with automatic libVLC fallback, hardware decoding, speed control, scaling, and frame-rate matching; automatic external-subtitle matching, character-set selection, and embedded or external audio tracks.
- **Save viewing progress:** resume, history, bookmarks, tags, notes, trash, and full backup and restore.
- **Interface themes:** multiple light palettes and a `#17191d` deep-gray dark theme across browsing, details, and playback.
- **Keep data on the device:** no account system, cloud sync, or telemetry; share credentials are encrypted locally and logs are redacted.
- **Optional poster tool:** generate posters and video metadata on a computer and write them into the share for the phone to read. No additional transcoding or media-management server is required.

## Interface

The screenshot below comes from the project's built-in anonymous gallery: no SMB connection, no private media, no real accounts or filenames.

<p align="center">
  <img src="./docs/images/gallery-preview.png" alt="Anonymous gallery preview" width="360" />
</p>

## Building it yourself

Only source code is published; there is no signed APK to download. Choose **Code → Download ZIP** on the repository page, extract it, and open PowerShell in the project root.

Install JDK 21, Android SDK 37, and Android Studio, then:

```powershell
cd LanPlay
.\gradlew.bat :app:assembleDebug --no-daemon --max-workers=2
```

The debug APK is written to `LanPlay/app/build/outputs/apk/debug/`. Release signing material is read only from environment variables or a configuration outside the workspace; nothing is committed.

Tests:

```powershell
cd LanPlay
.\gradlew.bat :app:testDebugUnitTest --no-daemon --max-workers=2

cd ..\lanplay-scraper
.\.venv\Scripts\python.exe -m unittest -v
```

## Technical details

**SMB 2/3 is implemented natively.** No WebDAV bridge, no third-party gateway, no companion service on the desktop. The folder you right-clicked and shared in Windows *is* the data source.

**Two playback engines, switched automatically.** Media3 covers almost everything; anything it can't decode falls back to libVLC without the user needing to know it happened. Hardware decoding, speed control, scaling, and frame-rate matching work on both.

**The scraper is a one-shot tool, not a daemon.** It runs on demand on a PC, writes posters and metadata back into the share, and then you close it. The phone reads the result over SMB — so nothing in this project needs to stay running 24/7. Real directories only ever go into the git-ignored `config.toml`.

**Credentials and logs are treated as sensitive.** SMB credentials are encrypted on-device, and logs are redacted before output rather than printing share paths and usernames verbatim.

Scraper installation, configuration, and network boundaries are documented in [`lanplay-scraper/README.md`](./lanplay-scraper/README.md).

## Layout

```text
LanPlay/             Android app and Gradle project
lanplay-scraper/     Optional Windows / Python metadata scraper
docs/images/         Redacted previews and the social preview
播放器规格.md         Implemented product and technical specification
设计系统.md           Visual, layout, and interaction specification
需求文档.md           Full requirements and acceptance boundaries
```

## Real-world limits

- Playback quality depends on the SMB server itself, network conditions, vendor background policies, and device decoders — none of which the app controls.
- The optional scraper visits public third-party pages. Follow your local law and those sites' terms.
- The first public version has no signed APK; build it yourself.

## More documentation

[Install, upgrade, backup, restore, troubleshooting](./docs/OPERATIONS.md) · [Changelog](./CHANGELOG.md) · [Contributing](./CONTRIBUTING.md) · [Security policy](./SECURITY.md)

## License

The source is released under the [MIT License](./LICENSE). Third-party libraries, including libVLC, keep their own licenses and are not relicensed by this repository.
