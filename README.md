# TG Drive — Aplikasi Windows (Tauri)

Wrapper desktop untuk https://drive.gtg.my.id. UI 100% dari web (termasuk
Max Speed / jalur langsung) — aplikasi native hanya menangani **cek update**.

## Struktur

- `src/index.html` — splash + pemeriksa update (Bahasa Indonesia).
  Naikkan `APP_VERSION_CODE` (integer) tiap rilis; samakan `APP_VERSION_NAME`
  dengan `version` di `src-tauri/tauri.conf.json`.
- `src-tauri/` — proyek Tauri (Rust). Jendela WebView2 → drive.gtg.my.id.
- `.github/workflows/build.yml` — build otomatis di GitHub Actions.

## Cara rilis versi baru

1. Naikkan versi di 3 tempat:
   - `src/index.html` → `APP_VERSION_CODE` (+1) & `APP_VERSION_NAME`
   - `src-tauri/tauri.conf.json` → `package.version`
   - `src-tauri/Cargo.toml` → `version`
2. Commit + push ke GitHub.
3. Buka tab **Actions** → **Build Windows (.exe)** → **Run workflow**.
4. Tunggu selesai → download artifact `tgdrive-windows-setup`
   (file `TG Drive_1.0.0_x64-setup.exe`).
5. Upload file .exe ke VPS (folder apk, mis. `/home/tgdrive/app/apk/`).
6. Di panel **Admin → Aplikasi Windows**: isi version_code, version_name,
   dan URL (cth `/apk/tgdrive-setup-1.0.0.exe`) → Simpan.
7. User yang buka aplikasi lama otomatis ditawari update.

## Alur update di aplikasi

1. Splash → fetch `https://drive.gtg.my.id/api/app-version`
2. `windows.version_code` > versi lokal + ada URL → dialog
   "Update tersedia" → [Update sekarang] / [Lewati]
3. Download .exe ke folder Downloads (progress bar)
4. [Jalankan installer] → installer NSIS berjalan → aplikasi lama ditutup
5. Jika cek update gagal (offline) → langsung buka aplikasi

## Catatan

- Tanpa code signing: Windows SmartScreen menampilkan peringatan biru
  saat install pertama → user klik "More info → Run anyway". Normal
  untuk aplikasi sideload.
- WebView2: bawaan Windows 10/11 modern. Bila belum ada, user perlu
  install WebView2 Runtime (gratis, dari Microsoft).
- Ikon: `src-tauri/icons/` (dibuat dari ikon APK).
