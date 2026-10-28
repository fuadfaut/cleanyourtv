# Changelog

All notable changes to the CleanYourTV - Fiberhome HG680-P custom build will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

## [Custom HG680-P Fixes] - 2026-10-28

### Added
- **Multi-Layered Family Protection & Vulgar Content Filtering**:
  - **Smart Extension Filter**: Injected bytecode filters into `Idlix`, `LK21`, `PusatFilm`, and `MovieBox` extensions that automatically detect and drop adult/vulgar keywords (e.g. `18+`, `semi`, `erotic`, `adult`, `sensual`, `sex`, `sexy`, `bokep`, `porno`, `ecchi`, `hentai`, `vulgar`, `xxx`, `uncensored`, `jav`, `fetish`, `lust`, `orgasm`, `taboo`, `seduction`, `bikini`, `stripper`, `playboy`, `nude`, `naked`, `unrated`) from Home sections, search results, and category feeds.
  - **Network-Level Cloudflare Family DNS**: Configured system DNS to `1.1.1.3` and `1.0.0.3` (Cloudflare 1.1.1.3 for Families) in `/system/etc/install-recovery-2.sh` with iptables port 53 NAT redirection to block malware and adult websites at the network socket level.

### Fixed
- **Idlix Provider & Cloudflare Challenge Bypass**:
  - Investigated Idlix outage on CloudStream: discovered that `z2.idlixku.com` Cloudflare WAF drops OkHttp requests without a browser `User-Agent` (`HTTP 403 / Cloudflare Challenge`).
  - Patched `Idlix.cs3` extension bytecode to inject desktop Chrome `User-Agent` header into all Next.js API calls (`/api/movies`, `/api/series`, `/api/search`, `/api/watch/session/claim`), restoring instant `HTTP 200 OK` JSON responses.
  - Locked patched extension at `version: 99` in `manifest.json` to prevent automatic downgrading by CloudStream plugin manager.
- **System-Wide Let's Encrypt CA Root Certificate (`ISRG Root X1` & `X2`)**:
  - Resolved `CertPathValidatorException: Trust anchor for certification path not found` which broke TMDB poster image loading (`image.tmdb.org`) and Let's Encrypt stream sources on Android 6.0 Marshmallow.
  - Installed `6187b673.0` (ISRG Root X1) and `8794b4e3.0` (ISRG Root X2) into `/system/etc/security/cacerts/` with root permissions.

## [Custom HG680-P Fixes] - 2026-10-26

### Fixed
- **CloudStream Boot-Persistence & Anti-Disappear**:
  - Resolved fatal `PackageManager: Error unpackaging native libs for app, errorCode=-18` error that caused CloudStream to be dropped (`pkg=null`) by Android's package manager on reboot.
  - Converted CloudStream into a permanent **System App** at `/system/app/CloudStream/CloudStream.apk`.
  - Placed all 11 native ARM libraries (including the custom BoringSSL `libconscrypt_jni.so` patch) directly into `/system/app/CloudStream/lib/arm/`.
  - Leveraged Android's native `extractLibs = false` handling for read-only `/system` apps, guaranteeing complete immunity to disk corruption from abrupt power cuts and eliminating the need for `chattr +i`.
  - Verified persistence across multiple consecutive cold reboots with full preservation of user data and streaming repositories in `/data/data/com.lagradost.cloudstream3/`.

## [Custom HG680-P Edition] - 2026-10-25

### Added
- **Fiberhome HG680-P Dedicated Support**:
  - Full optimization for Fiberhome HG680-P (Board `p212`, SoC Amlogic S905X, Pulpstone Android 6.0.1 Marshmallow, Linux Kernel 3.14.29, 2GB RAM).
  - Added high-resolution product photography for Fiberhome HG680-P (`docs/devices/fiberhome-hg680p.jpg`).
- **Web Remote Controller (`fastkey v3`)**:
  - Ultra-low latency (~30 ms) hardware-level D-Pad and media key injection directly via `/dev/input/event*`.
  - Integrated **Virtual Text Keyboard**: type search queries on TV directly from smartphone browser.
  - Auto-detection for dynamic IP changes to keep the remote web interface in sync with router DHCP.
  - Embedded web daemon running natively on port 8080 (`http://<IP_STB>:8080`).
- **Kiosk Billing & Rental System**:
  - Flexible rental countdown timer with quick preset buttons (30m, 1h, 2h, 3h, custom minutes).
  - **Unlimited Mode** ("Tidak Ada Waktu / Unlimited") for free play or owner usage.
  - Fullscreen lock overlay triggered automatically on timer expiration: *"WAKTU HABIS — Istirahatlah yang cukup"*.
  - Hidden Kiosk application with PIN-protected unlock to prevent tampering or escaping into Android settings.
- **CloudStream 4.8.0 TV Leanback Integration & Fixes**:
  - Native binary patch for `libconscrypt_jni.so` (BoringSSL) fixing fatal `SIGABRT` crash caused by syscall 384 (`sys_getrandom`) on Linux kernel 3.14.29 by forcing clean fallback to `/dev/urandom`.
  - Converted CloudStream to permanent **System App** (`/system/app/CloudStream/`) with native libraries directly in `/system/app/CloudStream/lib/arm/`, completely eliminating PackageManager `errorCode=-18` boot scan failures caused by immutable attributes on `/data/app`.
  - 100% resilient across hard power-offs, abrupt power cuts, and cold reboots on read-only `/system`.
  - Pre-installed **Mega repository** and Indonesian streaming repository (**Idlix**, anime, drama, and movies).
  - TV Leanback layout configured with shortcut pinned to CleanYourTV dock.
- **Documentation**:
  - [`REMOTE_BILLING.md`](REMOTE_BILLING.md): Comprehensive operational guide for Web Remote, Billing System, and CloudStream deployment.
  - [`PANDUAN_PEMULIHAN_HG680P.md`](PANDUAN_PEMULIHAN_HG680P.md): Complete unbricking guide, Amlogic USB Burning Tool (UBT) instructions, and SD Card Bootcard creation guide.
