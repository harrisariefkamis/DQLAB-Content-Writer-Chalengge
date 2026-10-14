Project MINI SQL & Insight Bisnis: Cara Data Analyst Mengunkap Pola Delay Flight
harrisssq I Data Enthusiast

Misi Anda sebagai Analis Data
Anda baru saja bergabung dengan maskapai penerbangan besar sebagai analis data junior.

Pimpinan merasa frustrasi dengan meningkatnya keluhan pelanggan tentang penerbangan yang tertunda dan dibatalkan.

Manajer Anda meminta Anda dengan permintaan mendesak:

“Saya butuh jawaban. Apa yang terjadi dengan penerbangan kita? Bandara mana yang menyebabkan penundaan? Apakah pembatalan meningkat tajam pada rute atau waktu tertentu?”

Tugas Anda adalah menggunakan SQL untuk menggali data, membersihkannya, menganalisisnya, dan berbagi wawasan.

Dataset: sample schema (tabel flight delay)
sample schema table flight
Google Cloud BigQuery
Download CSV : dataset_flight_delay

Source Query free: query sql menggunakan google cloud BigQuery

aku udah buat query sql final disini. Gasss. tinggal kamu belajar aja!!!

-- DATA FLIGHT DELAY ---

SELECT * FROM `projectlharris.Data_Flight.data_flight`;

--- Step 1: Data Cleaning & Validation ---
# IS NULL, WHERE, basic filtering #
SELECT *
FROM `projectlharris.Data_Flight.data_flight`
WHERE actual_departure IS NULL 
   OR departure_delay IS NULL;

--- Step 2: Calculate Average Delay by Airline ---
# AVG(), GROUP BY, filtering with WHERE #
SELECT airline, ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY airline
ORDER BY avg_delay DESC;

--- Step 3: Analyze Delay by Time of Day ---
# EXTRACT(HOUR), CASE WHEN, grouping #
SELECT 
  CASE
    WHEN EXTRACT(HOUR FROM scheduled_departure) BETWEEN 5 AND 11 THEN 'Morning'
    WHEN EXTRACT(HOUR FROM scheduled_departure) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
  END AS time_of_day,
  ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY time_of_day
ORDER BY avg_delay DESC;

--- Step 4: Identify Most Delayed Airports ---
# GROUP BY, AVG(), airport-based comparison #
SELECT origin, ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY origin
ORDER BY avg_delay DESC;

--- Step 5: Cancellations by Airline ---
# Aggregation, COUNT(), SUM(), calculated columns #
SELECT 
  airline,
  COUNT(*) AS total_flights,
  SUM(cancelled) AS total_cancelled,
  ROUND(SUM(cancelled) * 100.0 / COUNT(*), 2) AS cancel_rate_percent
FROM `Data_Flight.data_flight`
GROUP BY airline
ORDER BY cancel_rate_percent DESC;

--- Step 6: Summary Dashboard View (Bonus) --
# Combine metrics into one query #

SELECT 
  airline,
  ROUND(AVG(departure_delay), 2) AS avg_delay,
  SUM(cancelled) AS cancelled_flights,
  COUNT(*) AS total_flights,
  ROUND(SUM(cancelled) * 100.0 / COUNT(*), 2) AS cancel_rate_percent
FROM `Data_Flight.data_flight`
GROUP BY airline
ORDER BY avg_delay DESC;

--- QUERY SQL FINAL PROJECT MINI ---
--- follow.tiktok aku @harrryyyst ---
--- kalau kamu sudah copy ini ---
--- save.latihan dlu! ----
Apa yang anda butuhkan diartikel ini:
✅ Tehnik dasar pembersihan data

✅ Fungsi Waktu & Tanggal

✅ Agregasi & Pengelompokan

✅ Wawasan Keterlambatan Waktu

Analisis SQL berbasis waktu adalah salah satu keterampilan yang paling dicari dalam peran analitik modern — proyek ini membantu Anda menggabungkan logika + waktu, seperti yang dilakukan analis di perusahaan teknologi, logistik, dan perjalanan terkemuka.

SQL bukan lagi sekadar bahasa query untuk mengambil data, tetapi sudah menjadi senjata utama Data Analyst untuk menghasilkan insight bisnis yang berdampak nyata. Dalam industri transportasi dan penerbangan, analisis keterlambatan penerbangan (flight delay) menjadi contoh nyata bagaimana SQL digunakan untuk mendukung pengambilan keputusan strategis.

Melalui studi kasus dataset penerbangan, artikel ini membahas bagaimana query SQL dapat digunakan untuk membersihkan data, mengukur performa maskapai, menganalisis pola keterlambatan berdasarkan waktu, hingga menyusun ringkasan metrik yang siap dijadikan dashboard.

Step by step membuat Project MINI SQL
1. Data Cleaning & Validation
Dalam analisis data hal utama yang anda lakukan adalah memastikan data dalam kondisi bersih dan valid. dengan menggunakan kondisi “IS NULL” dan “WHERE” query SQL dapat mengidentifikasi baris data yang memiliki nilai kosong pada kolom penting seperti actual_departure dan departure_delay.

Tahap ini sangat krusial karena data yang tidak lengkap dapat menghasilkan kesimpulan yang salah. dalam praktik kerja Data Analyst, data cleaning sering memakan waktu paling banyak, dan SQL memungkinkan proses ini dilakukan lebih cepat dan konsisten dibandingkan pemrosesan manual.

-- Step 1: Data Cleaning & Validation ---
# IS NULL, WHERE, basic filtering #
SELECT *
FROM `projectlharris.Data_Flight.data_flight`
WHERE actual_departure IS NULL 
   OR departure_delay IS NULL;
2. Mengukur Performa Maskapai dengan Rata-Rata Delay
Setelah data dibersihkan, analisis berlanjut dengan menghitung rata-rata keterlambatan penerbangan setiap maskapai menggunakan fungsi AVG() dan GROUP BY. dengan menyaring penerbangan yang tidak dibatalkan (`cancelled = 0`) hasil analisis menjadi lebih relevan dan adil.

Become a Medium member
Query ini membantu menjawab pertanyaan penting: Maskapai mana yang memiliki performa paling buruk dalam hal ketepatan waktu? Insight semacam ini dapat digunakan oleh manajemen untuk mengevaluasi kualitas layanan dan merancang strategi perbaikan operasional.

--- Step 2: Calculate Average Delay by Airline ---
# AVG(), GROUP BY, filtering with WHERE #
SELECT airline, ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY airline
ORDER BY avg_delay DESC;
3. Mengungkap Pola Delay Berdasarkan Waktu Keberangkatan
SQL juga memungkinkan analisis berbasis waktu dengan memanfaatkan `EXTRACT(HOUR)` dan `CASE WHEN` untuk mengelompokkan penerbangan ke dalam kategori pagi, siang, dan malam.

Pendekatan ini membuka peluang insight yang lebih dalam, seperti mengetahui jam keberangkatan mana yang paling sering mengalami keterlambatan. Informasi ini dapat membantu perusahaan mengoptimalkan jadwal penerbangan, manajemen kru, hingga perencanaan operasional bandara.

--- Step 3: Analyze Delay by Time of Day ---
# EXTRACT(HOUR), CASE WHEN, grouping #
SELECT 
  CASE
    WHEN EXTRACT(HOUR FROM scheduled_departure) BETWEEN 5 AND 11 THEN 'Morning'
    WHEN EXTRACT(HOUR FROM scheduled_departure) BETWEEN 12 AND 17 THEN 'Afternoon'
    ELSE 'Evening'
  END AS time_of_day,
  ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY time_of_day
ORDER BY avg_delay DESC;
4. Menemukan Bandara dan Rute dengan Risiko Keterlambatan Tinggi
Dengan mengelompokkan data berdasarkan bandara asal (`origin`) dan menghitung rata-rata delay, SQL dapat mengidentifikasi lokasi yang paling sering mengalami keterlambatan.

Insight ini dapat digunakan untuk mengalokasikan sumber daya, meningkatkan koordinasi operasional, atau bahkan menjadi dasar negosiasi kerja sama dengan otoritas bandara. di sinilah peran Data Analyst bukan hanya sebagai teknisi data, tetapi juga sebagai Problem Solver Bisnis.

--- Step 4: Identify Most Delayed Airports ---
# GROUP BY, AVG(), airport-based comparison #
SELECT origin, ROUND(AVG(departure_delay), 2) AS avg_delay
FROM `Data_Flight.data_flight`
WHERE cancelled = 0
GROUP BY origin
ORDER BY avg_delay DESC;
5. Analisis Pembatalan Penerbangan sebagai Indikator Risiko
Selain keterlambatan, tingkat pembatalan penerbangan juga dianalisis menggunakan `COUNT()`, `SUM()`, dan perhitungan persentase. dengan menghitung cancel rate, Data Analyst dapat mengidentifikasi maskapai dengan risiko pembatalan tertinggi.

Metrik ini sangat penting bagi stakeholder karena mencerminkan tingkat keandalan layanan serta potensi risiko reputasi perusahaan.

--- Step 5: Cancellations by Airline ---
# Aggregation, COUNT(), SUM(), calculated columns #
SELECT 
  airline,
  COUNT(*) AS total_flights,
  SUM(cancelled) AS total_cancelled,
  ROUND(SUM(cancelled) * 100.0 / COUNT(*), 2) AS cancel_rate_percent
FROM `Data_Flight.data_flight`
GROUP BY airline
ORDER BY cancel_rate_percent DESC;
6. Dari Query SQL ke Dashboard dan Decision-Making
Langkah terakhir menggabungkan berbagai metrik — rata-rata delay, jumlah pembatalan, dan total penerbangan — ke dalam satu query ringkasan. Hasilnya dapat langsung digunakan untuk membangun dashboard di Looker Studio atau dianalisis lanjutan di Excel.

Workflow ini menunjukkan bagaimana SQL menjadi jembatan antara data mentah dan keputusan bisnis berbasis insight

--- Step 6: Summary Dashboard View (Bonus) ---
# Combine metrics into one query #

SELECT 
  airline,
  ROUND(AVG(departure_delay), 2) AS avg_delay,
  SUM(cancelled) AS cancelled_flights,
  COUNT(*) AS total_flights,
  ROUND(SUM(cancelled) * 100.0 / COUNT(*), 2) AS cancel_rate_percent
FROM `Data_Flight.data_flight`
GROUP BY airline
ORDER BY avg_delay DESC;
Free repository github: final query sql data_flight_delay

Baca juga: Bootcamp Data Analyst with SQL & Python in Google Platform

Baca juga: Bootcamp Data Analyst With Excel

7. Saran & Rekomendasi
Mulai latih buat project dummy dengan kuasai beberapa tools seperti:

Excel,SQL,Python dan Looker Studio
Problem Solving
Data Understanding
Pahami Framework Bisnis
Story dibalik Chart
Pahami Insight & Rekomendasi
Evaluasi Keputusan Bisnis
Tetap Konsisten
Banyak perusahaan atau company besar nggak baca tabel, dashboard mana yang paling bagus.mereka mencari makna,dampak dan keputusan. Jika kamu ingin menguasai kemampuan mengolah data nyata dengan SQL, membangun insight bisnis, dan meningkatkan peluang karier sebagai Data Analyst.

Mulai perjalananmu sekarang melalui DQLab disini kamu nggak hanya belajar teori doang,tapi langsung handson dan dibantu oleh AiDQLab dengan studi case nyata dan sangat relevan dengan dunia kerja saat ini. Yuk, mulai sekarang ambil Bootcamp dan jadikan masa depan kamu lebih cerah! Daftar Bootcamp Data Analyst with SQL & Python in Google Platform. Jika butuh contoh lebih detail, beri tahu saya.tag aku.kalau kamu buat project.

#DataAnalyst #DataScience #SQLtutorial #Python #LookerStudio #Bootcamp

Sql
Python
Data Analysis
Data Science
Google Bigquery
