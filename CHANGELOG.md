# Changelog

All notable changes to the CleanYourTV - Fiberhome HG680-P custom build will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/).

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
  - Dynamic anti-reset reboot protection: `/system/etc/install-recovery-2.sh` automatically discovers dynamic `/data/app/com.lagradost.cloudstream3*` directory structures across reinstalls/updates and reapplies `chattr +i` protected library.
  - Added fast non-destructive recovery procedure for unexpected power cut / hard shut-off recovery where `packages.xml` parsing drops registration.
  - Pre-installed **Mega repository** and Indonesian streaming repository (**Idlix**, anime, drama, and movies).
  - TV Leanback layout configured with shortcut pinned to CleanYourTV dock.
- **Documentation**:
  - [`REMOTE_BILLING.md`](REMOTE_BILLING.md): Comprehensive operational guide for Web Remote, Billing System, and CloudStream deployment.
  - [`PANDUAN_PEMULIHAN_HG680P.md`](PANDUAN_PEMULIHAN_HG680P.md): Complete unbricking guide, Amlogic USB Burning Tool (UBT) instructions, and SD Card Bootcard creation guide.
