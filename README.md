# [cite_start]✈️ Mini Project SQL & Business Insights: Uncovering Flight Delay Patterns [cite: 1]

Selamat datang di repositori **Mini Project SQL: Analisis Pola Delay Penerbangan**. [cite_start]Proyek ini dirancang untuk mensimulasikan peran seorang **Junior Data Analyst** di sebuah maskapai penerbangan besar yang sedang menghadapi tantangan operasional berupa tingginya angka keterlambatan (*delay*) dan pembatalan (*cancellation*) penerbangan[cite: 1, 2].

[cite_start]Proyek ini berfokus pada pembersihan data (*data cleaning*), manipulasi waktu, agregasi data, hingga penarikan wawasan bisnis (*business insights*)[cite: 4, 15, 16].

---

## 📌 Latar Belakang & Misi Analisis

### Skenario Bisnis
[cite_start]Manajemen maskapai merasa frustrasi dengan meningkatnya keluhan pelanggan terkait penerbangan yang tertunda dan dibatalkan[cite: 2]. [cite_start]Sebagai Data Analyst, Anda diminta untuk menjawab tantangan mendesak dari Manajer Operasional[cite: 3]:

> [cite_start]**"Saya butuh jawaban segera. Apa yang sebenarnya terjadi dengan penerbangan kita? Bandara mana yang menjadi biang kerok penundaan? Apakah pembatalan meningkat tajam pada rute atau waktu tertentu?"** [cite: 3]

### Goals
1. [cite_start]Memastikan data operasional bersih dan valid sebelum dianalisis[cite: 20].
2. [cite_start]Mengidentifikasi performa ketepatan waktu berdasarkan maskapai (*airline*)[cite: 25, 27].
3. [cite_start]Mengungkap pola keterlambatan berdasarkan waktu keberangkatan (*time of day*)[cite: 30, 31].
4. [cite_start]Memetakan bandara asal (*origin*) dengan risiko penundaan tertinggi[cite: 34].
5. [cite_start]Menyajikan metrik ringkas yang siap diintegrasikan ke Dashboard[cite: 42, 43].

---

## 🛠️ Tools & Technologies yang Digunakan

[cite_start]Dalam proyek ini, digunakan kombinasi beberapa *tools* modern yang menjadi standar industri untuk mengolah data hingga menghasilkan keputusan bisnis[cite: 16, 46, 47]:

* [cite_start]**Google Cloud BigQuery**: Digunakan sebagai platform *data warehouse* utama untuk menyimpan, mengelola, dan mengeksekusi query data penerbangan dalam skala besar[cite: 5, 52].
* [cite_start]**SQL (Standard SQL)**: Bahasa utama yang digunakan untuk proses penyaringan (*filtering*), pembersihan (*data cleaning*), manipulasi waktu, serta agregasi metrik bisnis[cite: 4, 15, 16, 52].
* [cite_start]**Looker Studio**: Alat visualisasi data yang direkomendasikan untuk mentransformasikan hasil query konsolidasi menjadi dashboard interaktif yang scannable bagi stakeholder[cite: 43, 46, 52, 61].
* [cite_start]**Microsoft Excel**: Digunakan sebagai opsi alternatif untuk melakukan analisis lanjutan, membuat laporan cepat, atau *cross-checking* data operasional[cite: 43, 46].
* [cite_start]**Python**: Menjadi kapabilitas tambahan tepercaya untuk pengembangan tingkat lanjut (*advanced data analysis*) di dalam ekosistem platform analisis modern[cite: 46, 52].

---

## 📊 Dataset & Skema Penataan

* [cite_start]**Platform:** Google Cloud BigQuery [cite: 5]
* [cite_start]**Dataset:** `projectlharris.Data_Flight.data_flight` [cite: 6]

Beberapa kolom kunci yang dianalisis meliputi:
* [cite_start]`airline`: Kode atau nama maskapai penerbangan[cite: 9].
* [cite_start]`scheduled_departure`: Jadwal waktu keberangkatan resmi[cite: 10].
* [cite_start]`actual_departure`: Waktu keberangkatan riil di lapangan[cite: 21].
* [cite_start]`departure_delay`: Durasi keterlambatan penerbangan dalam satuan menit[cite: 21].
* [cite_start]`origin`: Bandara asal keberangkatan[cite: 34].
* [cite_start]`cancelled`: Status pembatalan penerbangan (1 = Batal, 0 = Berangkat).

---

## 🚀 Tahapan Analisis & Query SQL

### Step 1: Data Cleaning & Validation
[cite_start]Memastikan validitas data dengan menyaring baris yang memiliki nilai kosong (*NULL*) pada kolom krusial agar tidak mendistorsi hasil analisis[cite: 21, 22].

```sql
-- Mengidentifikasi data kosong pada kolom keberangkatan
SELECT * FROM `projectlharris.Data_Flight.data_flight` 
WHERE actual_departure IS NULL 
   OR departure_delay IS NULL;
