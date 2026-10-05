Final Project - PPKD Jakarta Barat AI Bootcamp (PPKD AIAE x Hacktiv8)

Author: Fajrin Efantri

https://fajrinefantri.app.n8n.cloud/workflow/gp2q5380kJUYa0Xh

<img width="729" height="339" alt="image" src="https://github.com/user-attachments/assets/98c38646-ced6-41cb-8214-98704345146f" />


# Automasi Pembuatan Konten dengan n8n & AI

Workflow otomatisasi berbasis **n8n** yang berfungsi sebagai "Asisten Content Planner" cerdas untuk *brand* Es Kopi Kekinian Bernama "KOPI AH". Sistem ini menggunakan **Google Gemini AI** untuk melakukan *brainstorming*, menyusun ide konten (hook, caption, visual), mendata hasilnya ke **Google Sheets**, dan mengirimkan laporan real-time via **Telegram Bot**.

## Fitur Utama

Sistem ini menggunakan arsitektur **"Split & Merge"** di mana terdapat dua pemicu (Trigger) berbeda yang berujung pada satu eksekusi akhir yang sama:

1. ** On-Demand Mode (Telegram Trigger)**
   * User dapat memberikan instruksi spesifik kapan saja melalui chat Telegram (contoh: *"Buatkan konten tentang promo kopi gayo"*).
   * AI akan memproses permintaan tersebut secara instan.
2. ** Autopilot Mode (Schedule Trigger dengan Tema Dinamis)**
   * Berjalan otomatis 3 kali sehari tanpa intervensi manusia.
   * **Pukul 07:00:** Menghasilkan konten bertema *Motivasi Ngantor & Penyemangat Pagi*.
   * **Pukul 13:00:** Menghasilkan konten bertema *Jokes Ngantuk & Butuh Kafein*.
   * **Pukul 19:00:** Menghasilkan konten bertema *Lifestyle Nongkrong & Chill Abis Kerja*.
3. ** Terintegrasi Penuh (Google Sheets & Telegram)**
   * Output AI distandarisasi secara ketat dalam format JSON.
   * Data diproses dan diinput otomatis ke dalam kolom-kolom Google Sheets.
   * Notifikasi hasil pengerjaan dikirimkan kembali ke grup/chat Telegram tim.

---

## Arsitektur Workflow

Workflow dibangun menggunakan pendekatan *Multi-Branching* yang efisien:

* **Jalur Otomatis:** `Schedule Trigger` ➡️ `Switch Node` (Pengecekan jam: 7, 13, 19) ➡️ `3x Edit Fields` (Injeksi variabel tema) ➡️ `AI Agent (Gemini)`.
* **Jalur Manual:** `Telegram Trigger` ➡️ `AI Agent (Gemini)`.
* **Merge Point:** Hasil dari kedua jalur masuk ke ➡️ `Edit Fields (Parse JSON)` ➡️ `Google Sheets (Append Row)` ➡️ `Telegram Action (Send Message)`.

---

## Prasyarat Sistem

Untuk menjalankan atau mereplikasi workflow ini, Anda membutuhkan:
1. **n8n Instance** (Lokal atau Cloud).
2. **Telegram Bot Token** (Dari BotFather).
3. **Google Service Account** (Untuk integrasi Google Sheets).
4. **Google Gemini API Key** (Untuk node AI Agent).

---

## Struktur Output JSON AI

Prompt AI (System Message) telah dikunci secara ketat untuk hanya menghasilkan output JSON yang siap di-*parsing* ke *database*. Berikut adalah skemanya:

```json
{
  "nama_konten": "(Kategori pilar konten, misal: Edukasi, Hiburan)",
  "judul": "(Kalimat 'Hook' maksimal 7 kata)",
  "poin_utama": "(Penjelasan singkat ide konten dalam 2 kalimat)",
  "saran_visual": "(Deskripsi arahan visual/video yang estetik)",
  "caption": "(Caption siap *posting* lengkap dengan Call to Action)",
  "hashtag": "(Minimal 5 hashtag relevan)"
}
