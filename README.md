# ✈️ Mini Project SQL & Business Insights: Uncovering Flight Delay Patterns

https://harrisariefkamis.medium.com/list/project-mini-sql-b930d93ff1aa

**Completed as part of the DQLab Content Writer Challenge**

Mini Project SQL: Analisis Pola Delay Penerbangan. Proyek ini dirancang untuk mensimulasikan peran seorang Junior Data Analyst di sebuah maskapai penerbangan besar yang sedang menghadapi masalah operasional akibat banyaknya keterlambatan dan pembatalan penerbangan.

Proyek ini berfokus pada pembersihan data, manipulasi waktu, agregasi data, serta penarikan wawasan bisnis yang dapat digunakan untuk pengambilan keputusan strategis.

---

## 📌 Latar Belakang & Misi Analisis

### Skenario Bisnis

Manajemen maskapai merasa frustrasi dengan meningkatnya keluhan pelanggan terkait penerbangan yang tertunda dan dibatalkan. Sebagai Data Analyst, Anda diminta untuk menganalisis kinerja operasional maskapai dan mengidentifikasi sumber utama masalah delay.

> "Saya butuh jawaban segera. Apa yang sebenarnya terjadi dengan penerbangan kita? Bandara mana yang menjadi biang kerok penundaan? Apakah pembatalan meningkat tajam pada rute atau waktu tertentu?"

### Goals

1. Memastikan data operasional bersih dan valid sebelum dianalisis.
2. Mengidentifikasi performa ketepatan waktu berdasarkan maskapai.
3. Mengungkap pola keterlambatan berdasarkan waktu keberangkatan.
4. Memetakan bandara asal dengan risiko penundaan tertinggi.
5. Menyajikan metrik ringkas yang siap diintegrasikan ke dashboard.

---

## 🛠️ Tools & Technologies yang Digunakan

Dalam proyek ini, digunakan kombinasi beberapa tools modern yang umum digunakan dalam ekosistem analisis data untuk mengolah data hingga menghasilkan keputusan bisnis.

- Google Cloud BigQuery: Digunakan sebagai platform data warehouse utama untuk menyimpan, mengelola, dan mengeksekusi query data penerbangan dalam skala besar.
- SQL (Standard SQL): Bahasa utama yang digunakan untuk proses filtering, data cleaning, manipulasi waktu, serta agregasi metrik bisnis.
- Looker Studio: Alat visualisasi data yang direkomendasikan untuk mentransformasikan hasil query konsolidasi menjadi dashboard interaktif yang mudah dipahami stakeholder.
- Microsoft Excel: Digunakan sebagai alternatif untuk analisis lanjutan, membuat laporan cepat, atau cross-checking data operasional.
- Python: Menjadi kemampuan tambahan untuk analisis lanjutan dalam ekosistem data modern.

---

## 📊 Dataset & Skema Penataan

- Platform: Google Cloud BigQuery
- Dataset: `projectlharris.Data_Flight.data_flight`

Beberapa kolom kunci yang dianalisis meliputi:

- `airline`: Kode atau nama maskapai penerbangan.
- `scheduled_departure`: Jadwal waktu keberangkatan resmi.
- `actual_departure`: Waktu keberangkatan riil di lapangan.
- `departure_delay`: Durasi keterlambatan penerbangan dalam satuan menit.
- `origin`: Bandara asal keberangkatan.
- `cancelled`: Status pembatalan penerbangan (1 = batal, 0 = berangkat).

---

## 🚀 Tahapan Analisis & Query SQL

### Step 1: Data Cleaning & Validation

Memastikan validitas data dengan menyaring baris yang memiliki nilai kosong pada kolom krusial agar tidak mendistorsi hasil analisis.

```sql
-- Mengidentifikasi data kosong pada kolom keberangkatan
SELECT *
FROM `projectlharris.Data_Flight.data_flight`
WHERE actual_departure IS NULL
   OR departure_delay IS NULL;
```

### Step 2: Mengukur Performa Maskapai (Rata-Rata Delay)

Menghitung rata-rata keterlambatan per maskapai untuk mengevaluasi kualitas layanan ketepatan waktu. Hanya menghitung penerbangan yang berhasil berangkat (`cancelled = 0`).

```sql
SELECT
  airline,
  ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `projectlharris.Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY airline
ORDER BY avg_delay DESC;
```

### Step 3: Mengungkap Pola Delay Berdasarkan Waktu

Mengekstrak jam dari waktu keberangkatan menggunakan `EXTRACT(HOUR)` untuk mengelompokkannya menjadi Pagi, Siang, dan Malam.

```sql
SELECT
  CASE
    WHEN EXTRACT(HOUR FROM scheduled_departure) BETWEEN 5 AND 11 THEN 'Morning'
    WHEN EXTRACT(HOUR FROM scheduled_departure) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
  END AS time_of_day,
  ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `projectlharris.Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY time_of_day
ORDER BY avg_delay DESC;
```

### Step 4: Menemukan Bandara dengan Risiko Delay Tinggi

Mengidentifikasi bandara asal (`origin`) yang paling sering menyumbang penundaan jadwal akibat penumpukan atau kepadatan operasional.

```sql
SELECT
  origin,
  ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `projectlharris.Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY origin
ORDER BY avg_delay DESC;
```

### Step 5: Analisis Tingkat Pembatalan Penerbangan

Menghitung rasio pembatalan per maskapai menggunakan kombinasi fungsi `COUNT()` dan `SUM()` sebagai indikator risiko reputasi maskapai.

```sql
SELECT
  airline,
  COUNT(*) AS total_flights,
  SUM(cancelled) AS total_cancelled,
  ROUND(SUM(cancelled) * 100.0 / COUNT(*), 2) AS cancel_rate_percent
FROM `projectlharris.Data_Flight.data_flight`
GROUP BY airline
ORDER BY cancel_rate_percent DESC;
```

### Step 6: Summary Dashboard View (Metrik Konsolidasi)

Menyatukan semua metrik operasional utama ke dalam satu query tunggal yang siap dihubungkan ke data visualization tools.

```sql
SELECT
  airline,
  ROUND(AVG(departure_delay), 2) AS avg_delay,
  SUM(cancelled) AS cancelled_flights,
  COUNT(*) AS total_flights,
  ROUND(SUM(cancelled) * 100.0 / COUNT(*), 2) AS cancel_rate_percent
FROM `projectlharris.Data_Flight.data_flight`
GROUP BY airline
ORDER BY avg_delay DESC;
```

---

## 📈 Wawasan Bisnis & Rekomendasi (Key Takeaways)

Seorang Data Analyst bertindak sebagai problem solver bisnis dengan menerjemahkan hasil query menjadi langkah taktis operasional.

- Optimasi Jadwal (Time of Day): Membantu manajemen melihat slot jam yang paling sering mengalami delay, sehingga dapat menyusun ulang buffer time atau penjadwalan ulang operasional.
- Evaluasi Vendor & Bandara: Menjadi dasar bagi tim operasional untuk melakukan penyesuaian slot terbang atau negosiasi ulang layanan dengan otoritas bandara terkait.
- Mitigasi Risiko Reputasi: Menemukan akar masalah internal seperti teknis armada atau kesiapan kru pada maskapai dengan cancel rate tinggi untuk meminimalkan keluhan pelanggan.

---

## 💡 Oleh: DATA ENTHUSIATH

Halo! Saya **harrisssq**, seorang Data Enthusiast yang senang mengeksplorasi pengolahan data mentah menjadi keputusan bisnis yang berdampak nyata.

**Follow More Docs:**

- **TikTok:** [@harrryyyst](https://www.tiktok.com/@harrryyyst)
- **Certificate of Completion:** [CERTIFICATE of COMPLETION](https://drive.google.com/drive/folders/12r5FsE-yeFZDmRKLZicYwMLxG6rERozR?hl=ID)
- **Belajar Data Analytics:** Yuk, asah kemampuan hands-on kamu menggunakan studi kasus nyata industri seperti ini bersama [DQLab](https://dqlab.id).

Jangan lupa berikan ⭐ Star jika proyek latihan ini bermanfaat untuk portofolio kamu!
