# Project MINI: SQL & Business Insight — Mengungkap Pola Keterlambatan Penerbangan
Oleh: harrisssq | Data Enthusiast

## TL;DR
Proyek ini mencontohkan bagaimana seorang Data Analyst menggunakan SQL di Google BigQuery untuk membersihkan data penerbangan, menganalisis pola keterlambatan dan pembatalan, serta mengubah hasil analisis menjadi insight bisnis yang dapat dipakai untuk pengambilan keputusan operasional. Fokus utama proyek ini adalah memahami penyebab keterlambatan, mengidentifikasi bandara atau rute yang paling berisiko, serta mengevaluasi pola waktu dan tren pembatalan.

Dengan pendekatan yang terstruktur, analisis ini tidak hanya menyoroti angka, tetapi juga menjawab pertanyaan bisnis yang relevan: kapan delay paling sering terjadi, di kota mana risiko tertinggi, dan strategi apa yang bisa diterapkan untuk menurunkan tingkat gangguan operasional.

---

## 1. Latar Belakang & Misi
Di dunia maskapai, keterlambatan dan pembatalan penerbangan bukan sekadar masalah operasional, tetapi juga berdampak pada kepuasan pelanggan, efisiensi biaya, dan reputasi brand. Sebagai Data Analyst junior, Anda diberikan tugas untuk memecahkan masalah ini dengan pendekatan berbasis data.

Manajemen menerima keluhan yang cukup banyak terkait keterlambatan dan pembatalan. Tujuan Anda adalah memahami akar permasalahan secara lebih objektif, bukan hanya berdasarkan opini atau keluhan pelanggan. Analisis ini dilakukan untuk mengidentifikasi:

- penyebab utama keterlambatan,
- bandara atau rute yang paling berisiko,
- jam dan hari tertentu yang cenderung mengalami gangguan,
- serta pola pembatalan yang perlu segera ditangani.

Pertanyaan bisnis utama:
- Apa penyebab utama keterlambatan?
- Bandara atau rute mana yang paling sering mengalami delay?
- Apakah pembatalan meningkat pada jam atau rute tertentu?
- Bagaimana insight ini dapat mendukung keputusan operasional maskapai?

---

## 2. Dataset & Cara Akses
Dataset yang digunakan dalam analisis ini bersumber dari data penerbangan yang tersedia di Google Cloud BigQuery. Platform ini sangat cocok untuk menjalankan query SQL skala besar dan menganalisis pola data secara efisien.

### Sumber data
- Platform: Google Cloud BigQuery
- Tabel contoh: `projectlharris.Data_Flight.data_flight`
- Alternatif: dataset CSV `dataset_flight_delay` dapat diunduh untuk latihan di lingkungan lokal.

### Kolom kunci
Beberapa kolom penting yang akan sering digunakan dalam analisis ini antara lain:
- `airline`
- `scheduled_departure`
- `actual_departure`
- `departure_delay`
- `origin`
- `cancelled`
- `destination`
- `delay_minutes`
- `flight_date`

### Catatan penting
Sebelum melakukan analisis lanjutan, pastikan format tanggal dan zona waktu konsisten. Ini penting agar analisis berbasis jam atau hari tidak salah interpretasi, terutama jika dataset mencampurkan timezone atau format waktu yang berbeda.

---

## 3. Tools yang Digunakan
Dalam proses analisis, beberapa tools yang relevan digunakan untuk menjaga kualitas dan efisiensi pekerjaan:

- Google BigQuery — untuk eksplorasi data dan query SQL
- Looker Studio / Google Data Studio — untuk visualisasi hasil analisis
- Microsoft Excel / Google Sheets — untuk validasi dan pengecekan cepat
- Python (opsional) — untuk analisis lanjutan, data cleaning, atau automasi

Penggunaan kombinasi tools ini membantu memastikan bahwa insight yang dihasilkan bukan hanya akurat secara teknis, tetapi juga mudah dipahami oleh stakeholder bisnis.

---

## 4. Praktik & Langkah Analisis (Step-by-step)

### Step 0 — Preview data
Langkah pertama adalah melihat struktur data secara umum sebelum melakukan analisis lebih dalam.

```sql
SELECT *
FROM `projectlharris.Data_Flight.data_flight`
LIMIT 100;
```

Tujuan dari query ini adalah untuk memeriksa:
- nama kolom yang tersedia,
- tipe data masing-masing kolom,
- kemungkinan missing value,
- serta pola umum data yang akan dianalisis.

---

### Step 1 — Membersihkan dan memvalidasi data
Data penerbangan sering kali memiliki nilai kosong, format tanggal tidak seragam, atau data duplikat. Oleh karena itu, tahap pembersihan data sangat penting agar hasil analisis valid dan bisa dipertanggungjawabkan.

```sql
SELECT
  airline,
  origin,
  destination,
  scheduled_departure,
  actual_departure,
  departure_delay,
  cancelled,
  COUNT(*) AS total_records
FROM `projectlharris.Data_Flight.data_flight`
GROUP BY 1,2,3,4,5,6,7
ORDER BY total_records DESC;
```

Pada tahap ini, peneliti bisa melakukan pengecekan awal terhadap:
- apakah ada null value pada kolom penting,
- apakah delay sering terjadi pada asal atau tujuan tertentu,
- apakah ada pola yang jelas mengenai pembatalan penerbangan.

---

### Step 2 — Menghitung kinerja keterlambatan
Setelah data diperiksa dan dibersihkan, langkah berikutnya adalah mengukur tingkat keterlambatan secara agregat.

```sql
SELECT
  airline,
  ROUND(AVG(departure_delay), 2) AS avg_departure_delay,
  MAX(departure_delay) AS max_delay,
  SUM(CASE WHEN cancelled = 1 THEN 1 ELSE 0 END) AS total_cancelled_flights
FROM `projectlharris.Data_Flight.data_flight`
GROUP BY airline
ORDER BY avg_departure_delay DESC;
```

Query di atas membantu menjawab pertanyaan seperti:
- maskapai mana yang memiliki rata-rata delay paling tinggi?
- apakah terdapat hubungan antara delay dan jumlah pembatalan?
- apakah ada maskapai dengan performa operasional lebih stabil dibanding yang lain?

---

### Step 3 — Mengidentifikasi bandara dan rute yang paling berisiko
Analisis bandara atau rute sangat penting karena sering kali delay bukan terjadi secara acak. Ada faktor spesifik seperti cuaca, volume lalu lintas, atau keterlambatan pada koneksi antar penerbangan.

```sql
SELECT
  origin,
  COUNT(*) AS total_flights,
  AVG(departure_delay) AS avg_delay,
  SUM(CASE WHEN cancelled = 1 THEN 1 ELSE 0 END) AS total_cancelled
FROM `projectlharris.Data_Flight.data_flight`
GROUP BY origin
ORDER BY avg_delay DESC, total_cancelled DESC;
```

Hasil dari query ini sangat berguna untuk memberi rekomendasi operasional, misalnya:
- perlu penjadwalan ulang di bandara tertentu,
- fokus perbaikan pada rute dengan delay tinggi,
- atau mengevaluasi kapasitas resource di bandara yang paling sering terganggu.

---

### Step 4 — Analisis pembatalan berdasarkan jam dan rute
Keterlambatan dan pembatalan sering terjadi pada jam-jam tertentu, terutama saat puncak operasional atau kondisi cuaca tidak mendukung.

```sql
SELECT
  EXTRACT(HOUR FROM scheduled_departure) AS departure_hour,
  COUNT(*) AS total_flights,
  SUM(CASE WHEN cancelled = 1 THEN 1 ELSE 0 END) AS cancelled_flights,
  ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `projectlharris.Data_Flight.data_flight`
GROUP BY departure_hour
ORDER BY departure_hour;
```

Dengan query tersebut, Anda dapat melihat:
- jam berapa pembatalan cenderung meningkat,
- jam berapa delay paling tinggi,
- apakah pola tersebut konsisten untuk semua rute atau hanya untuk rute tertentu.

---

## 5. Interpretasi Insight Bisnis
Setelah analisis dilakukan, insight yang ditemukan harus dikaitkan dengan keputusan bisnis. Misalnya:

- Jika delay tertinggi terjadi di bandara tertentu, perusahaan dapat meninjau kapasitas terminal, jadwal ground handling, dan sistem koordinasi antar departemen.
- Jika pembatalan naik pada jam tertentu, kemungkinan ada overload pada puncak operasional atau keterbatasan sumber daya.
- Jika beberapa rute menunjukkan pola delay yang konsisten, maka evaluasi jadwal dan pemetaan armada menjadi langkah yang relevan.

Pada level bisnis, hasil analisis ini bukan hanya sekadar laporan statistik. Ia menjadi dasar untuk strategi perbaikan kualitas layanan, keputusan operasional, dan komunikasi dengan stakeholder.

---

## 6. Visualisasi yang Disarankan
Untuk mempermudah pemahaman, hasil analisis dapat divisualisasikan dalam bentuk:

- bar chart rata-rata delay per bandara,
- line chart pembatalan menurut jam keberangkatan,
- heatmap rute vs waktu,
- dashboard KPI performa operasional maskapai.

Visualisasi sangat penting karena selain membuat insight lebih mudah dibaca, juga membantu presenter menyampaikan hasil ke eksekutif atau pihak manajemen tanpa memerlukan pemahaman teknis yang mendalam.

---

## 7. Kesimpulan
Project MINI ini menunjukkan bahwa SQL bukan hanya alat untuk menampilkan data, tetapi juga alat untuk mengungkap pola, menemukan akar masalah, dan menyusun rekomendasi berbasis bukti. Dalam konteks industri penerbangan, kemampuan menganalisis delay dan cancellation secara terstruktur adalah keterampilan yang sangat relevan.

Dengan pendekatan yang tepat, seorang Data Analyst dapat menjembatani kebutuhan bisnis dan analisis data, menghasilkan insight yang tidak hanya informatif, tetapi juga actionable. Pada akhirnya, keputusan yang diambil akan lebih efektif jika didasarkan pada data yang valid, visualisasi yang jelas, dan interpretasi yang tepat.

---

## 8. Rekomendasi untuk Pengembangan Lebih Lanjut
Agar analisis menjadi lebih lengkap, beberapa pengembangan berikut layak dilakukan:

- menggabungkan dataset cuaca untuk melihat kecenderungan delay akibat faktor eksternal,
- menambahkan kolom hari kerja, hari libur, atau musim untuk melihat pola musiman,
- membandingkan performa tiap maskapai atau bandara secara kronologis,
- menerapkan dashboard interaktif agar stakeholder bisa mengeksplorasi data secara mandiri.

Dengan tahapan ini, proyek mini ini dapat berkembang menjadi analisis yang lebih matang dan siap dijadikan portfolio data analytics yang kuat.

---

## Penutup
Proyek ini memberikan gambaran praktis tentang bagaimana data dapat digunakan untuk menjawab permasalahan industri nyata. Bukan hanya soal angka, tetapi juga soal bagaimana data digunakan untuk menyelesaikan masalah bisnis yang berdampak langsung pada pengalaman pelanggan dan operasional perusahaan.

Jika Anda tertarik, langkah berikutnya adalah mengubah hasil analisis ini menjadi laporan yang lebih formal, atau mengembangkan dashboard interaktif yang siap ditampilkan ke audiens bisnis.
