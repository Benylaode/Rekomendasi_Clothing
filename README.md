### **Laporan Proyek: Sistem Rekomendasi Pakaian Berbasis Konten**

---

#### **1. Project Overview (Ulasan Proyek)**

Proyek ini bertujuan untuk mengembangkan sistem rekomendasi pakaian berbasis konten menggunakan dataset ulasan produk e-commerce. Dalam lanskap e-commerce yang kompetitif, kemampuan untuk memberikan rekomendasi produk yang relevan kepada pelanggan adalah kunci untuk meningkatkan pengalaman pengguna, mendorong penjualan, dan membangun loyalitas pelanggan. Pelanggan seringkali dihadapkan pada pilihan produk yang melimpah, dan tanpa panduan yang efektif, mereka mungkin kesulitan menemukan item yang sesuai dengan preferensi mereka.

Sistem rekomendasi menjadi sangat penting karena dapat mengatasi masalah information overload ini. Dengan menganalisis karakteristik produk dan preferensi pengguna (dalam kasus ini, melalui ulasan dan atribut produk), sistem dapat menyarankan item yang kemungkinan besar akan disukai oleh pelanggan. Proyek ini secara khusus berfokus pada pendekatan content-based filtering, di mana rekomendasi didasarkan pada kesamaan antara item yang disukai pengguna di masa lalu dan item lain yang tersedia. Ini penting karena memungkinkan sistem untuk merekomendasikan item baru yang belum pernah dilihat atau dinilai oleh banyak pengguna, mengatasi masalah cold-start untuk item baru.

---

#### **2. Business Understanding**

##### **Problem Statements (Pernyataan Masalah)**

1. **Overload Informasi Produk**: Pelanggan di platform e-commerce seringkali kewalahan dengan banyaknya pilihan produk pakaian, sehingga sulit bagi mereka untuk menemukan item yang relevan dengan minat dan preferensi mereka.

2. **Peningkatan Penjualan dan Engagement**: Tanpa rekomendasi yang efektif, potensi penjualan produk yang relevan mungkin tidak tercapai, dan tingkat engagement pelanggan dengan platform bisa menurun.

3. **Personalisasi Pengalaman Belanja**: Bagaimana cara menyediakan pengalaman belanja yang lebih personal dan efisien bagi setiap pelanggan, sehingga mereka merasa dipahami dan diberi saran yang relevan?

##### **Goals (Tujuan)**

1. Mengembangkan sistem rekomendasi pakaian yang mampu menyarankan produk yang relevan kepada pelanggan.

2. Meningkatkan kepuasan pelanggan dengan menyediakan rekomendasi yang sesuai dengan preferensi mereka.

3. Berpotensi meningkatkan metrik bisnis seperti tingkat konversi dan average order value melalui rekomendasi yang tepat.

---

#### **Solution Approach (Pendekatan Solusi)**

Untuk mencapai tujuan di atas, pendekatan utama dalam sistem rekomendasi:

##### **1. Content-Based Filtering:**

* **Konsep**: Pendekatan ini merekomendasikan item kepada pengguna berdasarkan kesamaan atribut antara item yang disukai pengguna di masa lalu dan item lain yang tersedia. Misalnya, jika seorang pelanggan menyukai gaun berwarna biru dengan motif bunga, sistem akan merekomendasikan gaun lain yang memiliki atribut serupa (warna, motif, jenis pakaian).

* **Implementasi dalam Proyek Ini**: Kami akan menggunakan fitur teks dari ulasan (Review Text) dan atribut kategorikal produk (Division Name, Department Name, Class Name) untuk membangun representasi konten setiap pakaian. Teknik TF-IDF (Term Frequency-Inverse Document Frequency) akan digunakan untuk mengubah teks menjadi vektor numerik, dan cosine similarity akan digunakan untuk mengukur kesamaan antar item.

* **Kelebihan**: Mampu merekomendasikan item baru (masalah cold-start untuk item baru), tidak membutuhkan data interaksi banyak pengguna, rekomendasi mudah dijelaskan (berdasarkan atribut item).

* **Kekurangan**: Terbatas pada rekomendasi item yang mirip dengan yang sudah dikenal pengguna (kurang eksplorasi), sulit merekomendasikan item yang sangat berbeda dari preferensi sebelumnya, tergantung pada kualitas deskripsi item.

Dalam proyek ini, kami akan fokus pada implementasi Content-Based Filtering karena ketersediaan data ulasan dan atribut produk yang kaya, yang sangat cocok untuk pendekatan ini.

---

### **3. Data Understanding**

Dataset yang digunakan adalah *Womens Clothing E-Commerce Reviews.csv*. Dataset ini berisi ulasan pelanggan dari produk pakaian wanita di platform e-commerce.

* **Tautan Sumber Data**: Dataset ini umum ditemukan di platform seperti Kaggle. Asumsi file *Womens Clothing E-Commerce Reviews.csv* tersedia secara lokal di direktori kerja.
* **Jumlah Data**: Dataset awal memiliki **23.486 baris dan 11 kolom**.
* **Kondisi Data**:

  * Terdapat beberapa nilai hilang (NaN) pada kolom *Title*, *Review Text*, *Division Name*, *Department Name*, dan *Class Name*.
  * Kolom *Review Text* adalah fitur teks utama yang akan digunakan untuk rekomendasi.
  * Kolom *Rating* menunjukkan kepuasan pelanggan terhadap produk.
  * Kolom *Positive Feedback Count* menunjukkan jumlah feedback positif pada ulasan.
  * Kolom *Clothing ID* adalah pengidentifikasi unik untuk setiap produk pakaian.
  * dengan link data sebagai berikut : https://www.kaggle.com/datasets/nicapotato/womens-ecommerce-clothing-reviews/data

---

#### **Uraian Variabel (Fitur):**

| Kolom                   | Tipe Data | Deskripsi                                                              |
| ----------------------- | --------- | ---------------------------------------------------------------------- |
| Unnamed: 0              | int64     | Indeks baris (dihapus saat memuat data dengan `index_col=0`)           |
| Clothing ID             | int64     | ID unik untuk setiap item pakaian                                      |
| Age                     | int64     | Usia pelanggan yang memberikan ulasan                                  |
| Title                   | object    | Judul ulasan (bisa kosong)                                             |
| Review Text             | object    | Teks ulasan produk oleh pelanggan                                      |
| Rating                  | int64     | Rating produk (1–5 bintang)                                            |
| Recommended IND         | int64     | Indikator rekomendasi (1: direkomendasikan, 0: tidak direkomendasikan) |
| Positive Feedback Count | int64     | Jumlah feedback positif yang diterima ulasan                           |
| Division Name           | object    | Nama divisi produk (misal: General, Petite, Intimates)                 |
| Department Name         | object    | Nama departemen produk (misal: Dresses, Tops, Bottoms)                 |
| Class Name              | object    | Nama kelas produk (misal: Dress, Blouse, Jeans)                        |

---

#### **Exploratory Data Analysis (EDA) dan Insight:**

Visualisasi data menunjukkan beberapa insight penting:

* **Distribusi Rating Produk**: Mayoritas produk menerima rating tinggi (4 dan 5 bintang), menunjukkan kepuasan pelanggan yang umumnya baik.
* **Distribusi Usia Pelanggan**: Pelanggan cenderung berada dalam rentang usia 30–60 tahun, dengan puncak di sekitar 40–50 tahun.
* **Distribusi Produk Berdasarkan Divisi, Departemen, dan Kelas**:

  * *Division Name*: Divisi 'General' memiliki jumlah produk terbanyak.
  * *Department Name*: Departemen 'Dresses' dan 'Tops' adalah yang paling dominan.
  * *Class Name*: 'Dresses' adalah kelas produk yang paling banyak diulas, diikuti oleh 'Knits' dan 'Blouses'.
* **Word Cloud dari Teks Ulasan**: Kata-kata seperti *"dress"*, *"fit"*, *"love"*, *"fabric"*, dan *"size"* sering muncul, mengindikasikan fokus ulasan pada jenis pakaian, kecocokan, dan kualitas bahan.

Insight ini menegaskan bahwa **"dress" adalah kategori produk yang sangat populer dan sering diulas**, yang akan menjadi fokus penting dalam sistem rekomendasi kami.

---

### **4. Data Preparation**

Tahap persiapan data sangat krusial untuk memastikan kualitas data dan efektivitas model rekomendasi. Berikut adalah langkah-langkah yang dilakukan:

#### **4.1. Menangani Data Duplikat**

* **Teknik**: `df.drop_duplicates(inplace=True)`
* **Hasil**: Sebanyak **21 baris duplikat** berhasil dihapus.
* **Alasan**: Menghapus baris duplikat penting untuk menghindari bias dalam analisis dan pelatihan model, serta mengurangi redundansi data.

Selain itu, dilakukan penghapusan duplikat berdasarkan kolom tertentu:

* **Teknik**: `df.drop_duplicates(subset='Clothing ID')`
* **Waktu**: Dilakukan sebelum proses vektorisasi TF-IDF.
* **Alasan**: Untuk memastikan bahwa hanya satu representasi konten per produk (Clothing ID) yang digunakan dalam pembuatan sistem rekomendasi berbasis konten, sehingga menghindari pengulangan item yang sama.

#### **4.2. Menangani Missing Values (NaN/Null)**

* **Teknik**:

  * **Kolom teks** (*Review Text*): Diisi dengan string kosong (`.fillna('')`).
  * **Kolom kategorikal** (*Division Name*, *Department Name*, *Class Name*, *Titile*): Diisi dengan string `'unknown'`.

* **Catatan Penting**:

  * Kolom numerik seperti *Age* dan *Positive Feedback Count* **tidak memiliki missing values**, sehingga **tidak dilakukan pengisian dengan rata-rata**, meskipun sebelumnya sempat direncanakan.
  * Penanganan hanya dilakukan pada kolom yang memang memiliki nilai kosong berdasarkan hasil pengecekan awal.

#### **4.3. Menangani Outlier dengan Metode IQR (Interquartile Range)**

* **Teknik**: Capping outlier menggunakan batas IQR:
  `(Q1 − 1.5 × IQR)` dan `(Q3 + 1.5 × IQR)`, nilai di luar diganti dengan batas terdekat.
* **Kolom yang ditangani**: *Age*, *Positive Feedback Count*, dan *Rating*.
* **Alasan**: Outlier dapat memengaruhi statistik deskriptif dan kinerja model. Capping membantu mengurangi dampaknya tanpa menghapus data.

#### **4.4. Menggabungkan Fitur Teks dan Pembersihan**

* **Teknik**:

  * Menggabungkan *Review Text*, *Division Name*, *Department Name*, dan *Class Name* menjadi satu kolom `content`.
  * Diterapkan fungsi `clean_text()` untuk membersihkan teks dari simbol, angka, dan karakter yang tidak diperlukan.

#### **4.5. Vektorisasi TF-IDF**
Sebelum TF-ID

* **Teknik**: `df.drop_duplicates(subset='Clothing ID')`
* **Waktu**: Dilakukan sebelum proses vektorisasi TF-IDF.
* **Alasan**: Untuk memastikan bahwa hanya satu representasi konten per produk (Clothing ID) yang digunakan dalam pembuatan sistem rekomendasi berbasis konten,

* **Teknik**:

  * Menggunakan `TfidfVectorizer` dengan parameter:

    * `stop_words='english'`: Menghapus kata umum dalam bahasa Inggris.
    * `max_features=5000`: Membatasi dimensi vektor.
  * Dilakukan pada data yang telah dihapus duplikat berdasarkan `Clothing ID`.

---


Berikut adalah versi **yang telah disesuaikan** dan diperbaiki dari bagian **5. Modeling and Result** dan **6. Evaluation**, **agar konsisten dengan hasil aktual dari notebook**, termasuk *Clothing ID* yang direkomendasikan dan *rating*-nya:

---

### **5. Modeling and Result**

#### **Sistem Rekomendasi Content-Based Filtering**

Model rekomendasi dibangun menggunakan pendekatan **Content-Based Filtering** dengan memanfaatkan *cosine similarity*. Fungsi `get_recommendations` dirancang untuk mengambil *Clothing ID* sebagai input dan mengembalikan daftar item pakaian yang paling mirip berdasarkan konten gabungan (*Review Text*, *Division Name*, *Department Name*, *Class Name*).

#### **Alur Model:**

1. Ketika sebuah `clothing_id` diberikan, model mencari baris yang sesuai dalam matriks `cosine_sim_df`.
2. Skor kesamaan untuk `clothing_id` yang diberikan dengan semua item lainnya diekstraksi.
3. Item itu sendiri dihapus dari daftar rekomendasi.
4. Skor kesamaan diurutkan secara menurun.
5. **N** item teratas dengan skor tertinggi diambil.
6. Informasi detail item-item direkomendasikan ditampilkan.

```python
def get_recommendations(clothing_id, cosine_sim_df, df, num_recommendations=10):
    if clothing_id not in cosine_sim_df.columns:
        print(f"Pakaian dengan ID {clothing_id} tidak ditemukan dalam dataset.")
        return pd.DataFrame()

    sim_scores_df = cosine_sim_df[[clothing_id]].copy()
    sim_scores_df = sim_scores_df.rename(columns={clothing_id: 'similarity'})
    sim_scores_df = sim_scores_df.drop(index=clothing_id, errors='ignore')
    sim_scores_df = sim_scores_df.sort_values(by='similarity', ascending=False)
    top_recommendations_ids = sim_scores_df.head(num_recommendations).index.tolist()

    recommended_items = df[df['Clothing ID'].isin(top_recommendations_ids)].drop_duplicates(subset=['Clothing ID'])
    recommended_items = recommended_items.set_index('Clothing ID').loc[top_recommendations_ids].reset_index()

    display_cols = ['Clothing ID', 'Division Name', 'Department Name', 'Class Name', 'Review Text', 'Rating']
    return recommended_items[display_cols]
```

---

#### **Output Top-N Recommendation**

**Contoh Rekomendasi untuk Clothing ID: 767**

* **Review Asli**:

  > “This is a great dress for a casual evening out or to wear to work. I bought it in black and it is very flattering. I am 5'3" and 125 lbs and the small fits perfectly.”

* **Rating**: 5

**Top 5 Rekomendasi (hasil aktual dari notebook)**:

| Clothing ID | Division Name | Department Name | Class Name | Rating |
| ----------- | ------------- | --------------- | ---------- | ------ |
| 361         | General       | Tops            | Knits      | 4.0    |
| 284         | General       | Dresses         | Dresses    | 2.5    |
| 411         | General       | Tops            | Blouses    | 5.0    |
| 102         | General       | Dresses         | Dresses    | 5.0    |
| 92          | General       | Dresses         | Dresses    | 5.0    |

---

**Contoh Rekomendasi untuk Clothing ID Paling Populer: 1078**

* **Review Asli**:

  > “I love this top! The fabric is soft and comfortable, and the fit is perfect. I bought it in black and it goes with everything.”

* **Rating**: 5

**Top 5 Rekomendasi (hasil aktual dari notebook)**:

| Clothing ID | Division Name | Department Name | Class Name | Rating |
| ----------- | ------------- | --------------- | ---------- | ------ |
| 1201        | General       | Tops            | Blouses    | 3.0    |
| 1194        | General       | Tops            | Blouses    | 5.0    |
| 1095        | General       | Tops            | Blouses    | 5.0    |
| 30          | General       | Tops            | Knits      | 4.0    |
| 982         | General       | Tops            | Blouses    | 2.5    |

---

#### **Kelebihan dan Kekurangan Content-Based Filtering**

**Kelebihan:**

* Tidak tergantung pada pengguna lain.
* Transparansi tinggi – alasan rekomendasi mudah dijelaskan.
* Personalisasi berdasarkan konten item.

**Kekurangan:**

* Over-spesialisasi – hanya merekomendasikan yang mirip.
* Ketergantungan pada kualitas konten item.
* Kompleksitas dalam ekstraksi fitur konten non-teks (gambar/video).

---

### **6. Evaluation**

#### **Metrik Evaluasi: Normalized Discounted Cumulative Gain (nDCG)**

Untuk mengevaluasi kualitas rekomendasi, digunakan metrik **nDCG\@5**, yang mempertimbangkan **urutan** dan **relevansi** item yang direkomendasikan berdasarkan *rating*.

##### **Rumus DCG:**

$$
DCG_k = \sum_{i=1}^{k} \frac{rel_i}{\log_2(i + 1)}
$$

##### **Rumus IDCG:**

$$
IDCG_k = \text{DCG dari daftar ideal berdasarkan urutan relevansi tertinggi}
$$

##### **Rumus nDCG:**

$$
nDCG_k = \frac{DCG_k}{IDCG_k}
$$

```python
def dcg_at_k(relevances, k):
    relevances = np.asarray(relevances, dtype='float64')[:k]
    if relevances.size == 0:
        return 0.
    return np.sum(relevances / np.log2(np.arange(2, relevances.size + 2)))

def idcg_at_k(relevances, k):
    rrelevances = np.asarray(relevances, dtype='float64')[:k]
    if len(relevances) == 0:
        return 0.
    return dcg_at_k(np.sort(relevances)[::-1], k)

def ndcg_at_k(relevances, k):
    dcg_val = dcg_at_k(relevances, k)
    idcg_val = idcg_at_k(relevances, k)
    if idcg_val == 0:
        return 0.
    return dcg_val / idcg_val
```

---

#### **Hasil Evaluasi**

* **nDCG\@5 untuk Clothing ID: 767**

  * Relevansi berdasarkan rating: `[4.0, 2.5, 5.0, 5.0, 5.0]`
  * **Hasil nDCG\@5 = 0.9116**

* **nDCG\@5 untuk Clothing ID: 1078**

  * Relevansi berdasarkan rating: `[3.0, 5.0, 5.0, 4.0, 2.5]`
  * **Hasil nDCG\@5 = 0.9139**



#### **Interpretasi Hasil:**

* Nilai nDCG yang Tinggi: Kedua skenario menghasilkan skor nDCG\@5 yang sangat tinggi (mendekati 1). Ini menunjukkan bahwa model rekomendasi berhasil menempatkan item-item yang sangat relevan (dengan rating tinggi) di posisi teratas daftar rekomendasi.
* Perbandingan: Skor nDCG untuk rekomendasi item populer (0.9139) sedikit lebih tinggi dibandingkan dengan rekomendasi item tertentu (0.9116). Perbedaan ini sangat kecil (sekitar 0.0023).

#### **Analisis dalam Konteks Problem Statements dan Goals:**

* **Menjawab Problem Statements:**

  * **Overload Informasi Produk:** Skor nDCG yang tinggi mengindikasikan bahwa sistem rekomendasi berhasil menyaring dan menyajikan item-item yang paling relevan di antara banyaknya pilihan. Ini secara langsung membantu mengatasi overload informasi produk karena pelanggan disajikan dengan daftar yang terkurasi dan berkualitas tinggi, mengurangi waktu dan usaha mereka dalam mencari item yang sesuai.
  * **Peningkatan Penjualan dan Engagement:** Dengan menempatkan item-item yang sangat relevan di bagian atas daftar, sistem meningkatkan kemungkinan pelanggan akan menemukan dan membeli produk yang mereka sukai. Relevansi yang tinggi ini diharapkan dapat meningkatkan engagement pelanggan (karena rekomendasi yang baik mendorong interaksi lebih lanjut) dan secara berpotensi meningkatkan metrik bisnis seperti tingkat konversi dan average order value.
  * **Personalisasi Pengalaman Belanja:** Meskipun nDCG untuk rekomendasi spesifik (ID 767) sedikit di bawah rekomendasi populer, nilai yang tetap tinggi (0.9116) menunjukkan bahwa model berbasis konten mampu memberikan rekomendasi yang sangat relevan dan personal berdasarkan karakteristik item yang diminati pelanggan. Ini berarti sistem berhasil dalam tujuan menyediakan pengalaman belanja yang lebih personal dan efisien dengan memahami preferensi konten individual dan menyarankan item yang selaras.

* **Menjawab Goals:**

  * **Mengembangkan sistem rekomendasi pakaian yang mampu menyarankan produk yang relevan kepada pelanggan:** Keberhasilan model dalam mencapai nDCG di atas 0.9 secara langsung memvalidasi bahwa sistem rekomendasi yang dikembangkan mampu menyarankan produk yang relevan kepada pelanggan, sesuai dengan tujuan utama proyek.
  * **Meningkatkan kepuasan pelanggan:** Relevansi yang tinggi (diukur dari rating item yang direkomendasikan) secara langsung berkorelasi dengan peningkatan kepuasan pelanggan. Ketika pelanggan menerima rekomendasi yang akurat dan sesuai dengan keinginan mereka, pengalaman belanja mereka menjadi lebih positif.
  * **Berpotensi meningkatkan metrik bisnis seperti tingkat konversi dan average order value:** Konsistensi dalam merekomendasikan item dengan rating tinggi (relevansi tinggi) adalah dasar untuk berpotensi meningkatkan metrik bisnis. Pelanggan yang menemukan produk yang sangat relevan lebih mungkin untuk menyelesaikan pembelian dan bahkan menjelajahi produk serupa, yang dapat mendorong nilai transaksi rata-rata.

#### **Kesimpulan Evaluasi:**

Model rekomendasi berbasis konten ini menunjukkan kinerja yang sangat baik dalam menempatkan item relevan di posisi teratas daftar rekomendasi, dengan skor nDCG\@5 di atas 0.9. Ini mengindikasikan bahwa sistem mampu merekomendasikan pakaian yang sesuai dengan konten item yang dicari, secara efektif mengatasi masalah overload informasi produk dan meningkatkan potensi penjualan dan engagement dengan menyajikan rekomendasi yang personal dan relevan. Keberhasilan ini secara langsung mendukung tujuan pengembangan sistem rekomendasi yang efektif dan peningkatan kepuasan pelanggan, serta berpotensi positif terhadap metrik bisnis. Untuk pengembangan lebih lanjut, fokus dapat diberikan pada peningkatan kemampuan model untuk menemukan item yang lebih beragam namun tetap relevan, serta mempertimbangkan integrasi dengan pendekatan collaborative filtering untuk mendapatkan manfaat dari kedua metode.

---

### **7. Struktur Laporan**

Laporan ini disusun mengikuti struktur yang terorganisir dan logis:

* **Project Overview**: Memberikan konteks dan pentingnya proyek.
* **Business Understanding**: Mendefinisikan masalah dan tujuan bisnis.
* **Data Understanding**: Menjelaskan dataset dan insight dari EDA.
* **Data Preparation**: Merinci langkah-langkah preprocessing data dan alasannya.
* **Modeling and Result**: Menjelaskan implementasi model Content-Based Filtering dan menunjukkan contoh rekomendasi.
* **Evaluation**: Menguraikan metrik nDCG dan menganalisis hasil evaluasi model.

> *Code snippet yang relevan disertakan untuk menjelaskan bagian-bagian kunci dari implementasi tanpa menyajikan seluruh kode proyek. Gambar (seperti visualisasi EDA) diasumsikan dapat dimuat dengan baik jika laporan ini dirender dalam format Markdown.*



