# Project MINI: SQL & Business Insight — Mengungkap Pola Delay Penerbangan
Oleh: harrisssq | Data Enthusiast

## TL;DR
Proyek ini menunjukkan cara seorang Data Analyst menggunakan SQL (BigQuery) untuk membersihkan data, menganalisis pola keterlambatan dan pembatalan penerbangan, serta menyajikan metrik yang bisa langsung dipakai ke dashboard. Materi mencakup teknik pembersihan data, fungsi tanggal/waktu, agregasi, dan contoh query final.

---

## 1. Latar Belakang & Misi
Anda bergabung sebagai Data Analyst junior di sebuah maskapai besar. Manajemen menerima banyak keluhan terkait keterlambatan dan pembatalan penerbangan. Tugas Anda: temukan akar masalah — bandara, waktu, atau rute — dan sajikan rekomendasi berbasis data.

Pertanyaan bisnis utama:
- Apa penyebab utama keterlambatan?
- Bandara atau rute mana yang berisiko tinggi?
- Apakah pembatalan meningkat pada jam/rute tertentu?

---

## 2. Dataset & Cara Akses
- Platform: Google Cloud BigQuery  
- Tabel contoh: `projectlharris.Data_Flight.data_flight`  
- Alternatif: unduh CSV `dataset_flight_delay` untuk latihan lokal.

Kolom kunci:
- `airline`, `scheduled_departure`, `actual_departure`, `departure_delay`, `origin`, `cancelled`

Catatan: pastikan format waktu (timezone) konsisten saat melakukan analisis berbasis jam.

---

## 3. Tools yang Digunakan
- Google BigQuery (SQL)
- Looker Studio / Google Data Studio (visualisasi)
- Microsoft Excel / Google Sheets (cross-check / ad-hoc)
- Python (opsional, analisis lanjutan)

---

## 4. Praktik & Langkah Analisis (Step-by-step)

### Step 0 — Preview data
```sql
SELECT * FROM `projectlharris.Data_Flight.data_flight` LIMIT 100;
