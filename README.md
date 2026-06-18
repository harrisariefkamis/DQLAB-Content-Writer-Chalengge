# ✈️ Mini Project SQL & Business Insights: Uncovering Flight Delay Patterns

Selamat datang di repositori **Mini Project SQL: Analisis Pola Delay Penerbangan**. Proyek ini dirancang untuk mensimulasikan peran seorang **Junior Data Analyst** di sebuah maskapai penerbangan besar yang sedang menghadapi tantangan operasional berupa tingginya angka keterlambatan (*delay*) dan pembatalan (*cancellation*) penerbangan.

Proyek ini berfokus pada pembersihan data (*data cleaning*), manipulasi waktu, agregasi data, hingga penarikan wawasan bisnis (*business insights*) menggunakan **Google Cloud BigQuery**.

---

## 📌 Latar Belakang & Misi Analisis

### Skenario Bisnis
Manajemen maskapai merasa frustrasi dengan meningkatnya keluhan pelanggan terkait penerbangan yang tertunda dan dibatalkan. Sebagai Data Analyst, Anda diminta untuk menjawab tantangan mendesak dari Manajer Operasional:

> **"Saya butuh jawaban segera. Apa yang sebenarnya terjadi dengan penerbangan kita? Bandara mana yang menjadi biang kerok penundaan? Apakah pembatalan meningkat tajam pada rute atau waktu tertentu?"**

### Goals
1. Memastikan data operasional bersih dan valid sebelum dianalisis.
2. Mengidentifikasi performa ketepatan waktu berdasarkan maskapai (*airline*).
3. Mengungkap pola keterlambatan berdasarkan waktu keberangkatan (*time of day*).
4. Memetakan bandara asal (*origin*) dengan risiko penundaan tertinggi.
5. Menyajikan metrik ringkas yang siap diintegrasikan ke Dashboard (e.g., Looker Studio).

---

## 🛠️ Tech Stack & Dataset

* **Platform:** Google Cloud BigQuery
* **Bahasa Query:** Standard SQL
* **Dataset:** `projectlharris.Data_Flight.data_flight` (Sample Schema Flight Delay)

| Nama Kolom | Tipe Data | Deskripsi |
| :--- | :--- | :--- |
| `airline` | STRING | Kode/Nama maskapai penerbangan |
| `scheduled_departure` | TIMESTAMP | Jadwal waktu keberangkatan |
| `actual_departure` | TIMESTAMP | Waktu keberangkatan riil |
| `departure_delay` | FLOAT/INT | Durasi keterlambatan (dalam menit) |
| `origin` | STRING | Bandara asal keberangkatan |
| `cancelled` | INT | Status pembatalan (1 = Batal, 0 = Berangkat) |

---

## 🚀 Tahapan Analisis & Query SQL

### Step 1: Data Cleaning & Validation
Memastikan validitas data dengan menyaring baris yang memiliki nilai kosong (*NULL*) pada kolom krusial. Data yang tidak lengkap dapat mendistorsi hasil rata-rata delay.

```sql
-- Mengidentifikasi data kosong pada kolom keberangkatan
SELECT *
FROM `projectlharris.Data_Flight.data_flight`
WHERE actual_departure IS NULL 
   OR departure_delay IS NULL;

```

### Step 2: Mengukur Performa Maskapai (Rata-Rata Delay)

Menghitung rata-rata menit keterlambatan per maskapai untuk menyaring mana yang memiliki kinerja operasional paling buruk. Hanya menghitung penerbangan yang sukses berangkat (`cancelled = 0`).

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

Mengekstrak jam dari waktu keberangkatan menggunakan `EXTRACT(HOUR)` dan mengelompokkannya menjadi Pagi (*Morning*), Siang (*Afternoon*), dan Malam (*Evening*).

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

Mengidentifikasi bandara asal (`origin`) yang paling sering menyumbang penundaan jadwal akibat masalah eksternal atau kepadatan lalu lintas udara.

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

Menghitung rasio pembatalan (*cancel rate percent*) per maskapai menggunakan fungsi kombinasi `SUM` dan `COUNT` untuk melihat risiko operasional tertinggi.

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

Menyatukan semua metrik utama ke dalam satu query tunggal. Hasil dari query ini sangat siap untuk dihubungkan langsung ke *data visualization tools* seperti Looker Studio, Tableau, atau PowerBI.

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

Berdasarkan struktur query di atas, berikut adalah bagaimana seorang Data Analyst menerjemahkan data mentah menjadi nilai bisnis:

* **Optimasi Jadwal:** Melalui pengelompokan waktu (*Time of Day*), manajemen dapat melihat di slot jam berapa penumpukan delay terjadi (misal: penundaan tinggi di malam hari akibat efek domino dari jadwal pagi) untuk mengevaluasi ulang *buffer time* antar penerbangan.
* **Evaluasi Vendor & Bandara:** Bandara dengan rata-rata delay tertinggi (Hasil Step 4) menjadi dasar bagi tim legal dan operasional untuk melakukan negosiasi ulang kontrak layanan atau penyesuaian slot terbang dengan otoritas bandara terkait.
* **Mitigasi Risiko Reputasi:** Maskapai dengan *cancel rate* tinggi perlu diaudit secara internal guna mengetahui apakah pembatalan disebabkan oleh faktor teknis armada, kekurangan kru, atau kendala cuaca.

---

## 💡 Tentang Penulis

Halo! Saya **harrisssq**, seorang *Data Enthusiast* yang senang mengeksplorasi pengolahan data mentah menjadi keputusan bisnis yang berdampak nyata.

* **TikTok:** [@harrryyyst](https://www.google.com/search?q=https://www.tiktok.com/%40harrryyyst)
* **Belajar Data Analytics:** Yuk, asah kemampuan *hands-on* kamu menggunakan studi kasus nyata seperti ini bersama [DQLab](https://dqlab.id).

*Jangan lupa berikan ⭐ Star jika proyek latihan ini bermanfaat untuk portofolio kamu!*

```

```
