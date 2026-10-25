# Panduan Lengkap Pemulihan STB Fiberhome HG680-P (Amlogic S905X)

> Dokumen ini disusun khusus untuk memulihkan STB Fiberhome HG680-P milik **fuadfaut** yang mengalami bootloop setelah penonaktifan paket sistem. Semua berkas yang dibutuhkan telah dipersiapkan dan disalin ke Windows NVMe Drive.

---

## 1. Spesifikasi Lengkap Perangkat (Terverifikasi dari Backup Asli)

- **Model Perangkat**: Fiberhome HG680-P (Indihome)
- **Chipset / SoC**: **Amlogic S905X** (Meson GXL)
  - **CPU**: Quad-core ARM Cortex-A53 @ 1.5 GHz
  - **GPU**: Mali-450 MP (Penta-core)
- **Model Board**: **Amlogic `p212`** (`ro.board.platform: gxl`, variant: `2gs`)
- **RAM**: **2 GB DDR3** (`[ro.total.memsize]: [2G]`)
- **Penyimpanan Internal**: **8 GB eMMC**
- **Sistem Operasi Asli**: Android 6.0.1 Marshmallow (API Level 23, 32-bit `armeabi-v7a`)
- **Kustomisasi Awal**: Custom ROM Molly Player (Android TV build `20210203`)

---

## 2. Analisis & Akar Masalah Bootloop

### Mengapa STB Mengalami Bootloop?
Pada proses debloat sebelumnya, paket berikut dinonaktifkan:
1. `com.android.provision` (Setup & Status Penyelesaian Konfigurasi Android)
2. `eu.chainfire.supersu` (Daemon Root / SuperSU)
3. 24 paket aplikasi lainnya.

Saat booting:
- Kernel Linux dan `init` berhasil berjalan normal.
- Layar TV menampilkan teks *"Android"* lalu *"Optimizing app 1 to 50"* (kompilasi dex2oat).
- Begitu mencapai 50/50, `system_server` Android diinisialisasi.
- Karena `com.android.provision` dan `eu.chainfire.supersu` tidak aktif, `system_server` melemparkan *Fatal Exception* (Crash) yang menyebabkan framework Zygote mati dan me-restart perangkat secara berulang-ulang dalam siklus 30 detik.

### Mengapa Perlu USB Burning Tool di Windows?
- Chipset Amlogic S905X memiliki bootloader MaskROM tingkat rendah (**Hardware ID `1b8e:c003 GX-CHIP`**).
- Pada varian RAM 2GB, inisialisasi chip RAM membutuhkan kontrol register memori (`Control0` dan `Control1`) serta konfigurasi clock yang sangat spesifik.
- Aplikasi resmi **Amlogic USB Burning Tool v2.1.6** di Windows menangani protokol register pabrikan ini secara otomatis dan 100% stabil melalui driver resmi *WorldCup Device*.

---

## 3. Berkas & Lokasi Penyimpanan di Windows

Semua berkas telah disalin dan siap digunakan di drive Windows NVMe SSD Anda:

- **Lokasi Folder di Windows**:
  ```text
  C:\Users\fuadfaut\Desktop\RECOVERY_HG680P\
  ```
- **Isi Berkas di Dalam Folder**:
  1. `UBT_v2.1.6.exe`: Installer resmi Amlogic USB Burning Tool v2.1.6 + Driver.
  2. `ATV FW HG680P USB Burning V2.1.rar`: Arsip asli firmware Android TV.
  3. `ATV FW HG680P.img`: Berkas image firmware yang sudah diekstrak dan siap diimpor ke USB Burning Tool.
  4. Salinan seluruh repository kode, script pemulihan, dan backup data STB Anda.

---

## 4. Langkah-Langkah Pemulihan Bertahap (Step-by-Step di Windows)

### Langkah 1: Masuk ke Sistem Operasi Windows
1. Restart laptop Anda dari Linux Fedora.
2. Pada menu boot (GRUB/BIOS), pilih untuk masuk ke **Windows**.

### Langkah 2: Instalasi Amlogic USB Burning Tool
1. Di Desktop Windows, buka folder:
   ```text
   RECOVERY_HG680P
   ```
2. Klik kanan pada file **`UBT_v2.1.6.exe`**, pilih **Run as Administrator**.
3. Ikuti petunjuk instalasi di layar hingga selesai.
4. **Penting**: Saat muncul jendela instalasi driver (*WorldCup Device Driver / libusb*), klik **Install / Continue Anyway** untuk memastikan driver terpasang sempurna.

### Langkah 3: Konfigurasi USB Burning Tool
1. Buka aplikasi **USB Burning Tool** dari Start Menu atau Desktop (klik kanan -> **Run as Administrator**).
2. Jika tampilan awal berbahasa Mandarin:
   - Klik menu kedua dari kiri di baris paling atas: `View` (atau menu kedua) -> Pilih **English**.
3. Klik menu **File** -> **Import Image**.
4. Arahkan ke folder Desktop:
   ```text
   C:\Users\fuadfaut\Desktop\RECOVERY_HG680P\ATV FW HG680P.img
   ```
5. Tunggu beberapa detik hingga proses verifikasi berkas image selesai.

### Langkah 4: Pengaturan Keamanan Partisi (SANGAT PENTING)
Di panel sebelah kanan aplikasi USB Burning Tool, perhatikan bagian konfigurasi checkbox:
- [ ] **Erase bootloader** -> **HILANGKAN CENTANG (WAJIB UNCHECK)**!
  > **PERINGATAN KERAS**: Opsi *Erase bootloader* TIDAK BOLEH dicentang. Hal ini bertujuan untuk melindungi bootloader asli pabrik, menjaga MAC Address LAN/Wi-Fi agar tidak hilang, dan mencegah kerusakan partisi eMMC permanen.
- [x] **Erase flash**: Biarkan tercentang (Normal erase).
- [x] **Reset after success**: Biarkan tercentang.

### Langkah 5: Memulai Mode Flashing
1. Klik tombol **Start** di pojok kanan atas aplikasi USB Burning Tool.
2. Siapkan STB Fiberhome HG680-P:
   - Pastikan kabel LAN dan kabel HDMI **dilepas** terlebih dahulu.
   - Adaptor listrik 12V dalam kondisi **belum dicolokkan ke STB**.
3. Hubungkan kabel **USB Male-to-Male**:
   - Satu ujung colokkan ke port USB di Laptop.
   - Ujung lainnya colokkan ke port USB di STB (port USB yang tadi berhasil terdeteksi).
4. Colokkan adaptor daya 12V STB ke stopkontak listrik.
5. Dalam 1–3 detik, USB Burning Tool akan berbunyi *ding* dan menampilkan status:
   ```text
   [Hub X-X] Connect success
   ```
6. Bilah progres (progress bar) berwarna biru/hijau akan mulai bergerak dari 1% hingga 100% (proses penulisan partisi boot, system, dan recovery).

### Langkah 6: Penyelesaian & Pengujian
1. Tunggu sekitar 2–3 menit hingga bar menunjukkan **100%** dengan teks hijau:
   ```text
   Burning successfully
   ```
2. Klik tombol **Stop** di USB Burning Tool.
3. Tutup aplikasi USB Burning Tool.
4. Cabut kabel USB Male-to-Male dan cabut adaptor daya 12V dari STB.
5. Sambungkan kembali kabel HDMI dari STB ke TV, lalu colokkan adaptor listrik 12V untuk menyalakan STB.
6. **Booting Pertama**:
   - Booting pertama kali akan memakan waktu sekitar 2–3 menit karena sistem Android melakukan penyesuaian partisi dan *caching*.
   - STB akan masuk ke antarmuka **Android TV** yang segar, bersih, responsif, dengan akses Root dan ADB aktif kembali secara normal.

---

## 5. Ringkasan Keselamatan untuk Debloat Mendatang

Untuk mencegah kejadian serupa di masa mendatang:
- **Jangan pernah menonaktifkan**:
  - `com.android.provision` (Dibutuhkan framework Android untuk validasi status setup perangkat).
  - `eu.chainfire.supersu` (Dibutuhkan untuk izin akses root dan startup script).
  - Layanan sistem inti Google Play Services dan Package Installer.
- **Lakukan debloat secara bertahap (batch kecil)**: Maksimal 2–3 aplikasi per pengujian, lalu restart untuk memastikan stabilitas sistem sebelum melanjutkan ke aplikasi lainnya.
