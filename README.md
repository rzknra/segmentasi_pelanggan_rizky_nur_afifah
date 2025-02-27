# Laporan Proyek Machine Learning - Segmentasi Pelanggan

## Domain Proyek

Dalam dunia bisnis, memahami pelanggan merupakan salah satu kunci keberhasilan strategi pemasaran. Segmentasi pelanggan memungkinkan bisnis untuk mengelompokkan pelanggan berdasarkan perilaku mereka, sehingga dapat menyusun strategi yang lebih efektif. Salah satu pendekatan yang umum digunakan adalah metode **RFM (Recency, Frequency, Monetary)**, yang mengevaluasi pelanggan berdasarkan seberapa baru mereka bertransaksi (Recency), seberapa sering mereka bertransaksi (Frequency), dan seberapa banyak mereka membelanjakan uangnya (Monetary) (Kotler & Keller, 2015).

Namun, data RFM sering kali memiliki distribusi yang tidak normal dan rentang nilai yang bervariasi, yang dapat memengaruhi hasil analisis ketika menggunakan metode klasterisasi seperti **K-Means**. Oleh karena itu, diperlukan **transformasi data** seperti **Yeo-Johnson Transformation** untuk menormalkan distribusi data dan **standardisasi** menggunakan **StandardScaler** untuk memastikan fitur berada dalam skala yang sama sebelum melakukan klasterisasi (James dll, 2013).

Metode **K-Means Clustering** digunakan dalam proyek ini untuk mengelompokkan pelanggan berdasarkan karakteristik RFM mereka, sehingga dapat membantu dalam pengambilan keputusan strategis seperti retensi pelanggan dan pemasaran yang lebih personal (Han, Kamber, & Pei, 2011).

## Pemahaman Bisnis
Segmentasi pelanggan menggunakan metode RFM dan K-Means bertujuan untuk membantu bisnis dalam memahami pola perilaku pelanggan sehingga dapat mengoptimalkan strategi pemasaran dan meningkatkan retensi pelanggan. 

## Rumusan Masalah
1. **Bagaimana cara mengolah dan membersihkan data pelanggan** sebelum melakukan analisis segmentasi?  
2. **Bagaimana cara memastikan bahwa data yang digunakan memiliki distribusi yang lebih normal dan skala yang seragam?**  
3. **Berapa jumlah klaster optimal** yang sesuai dengan pola pembelian pelanggan?  
4. **Bagaimana karakteristik masing-masing segmen pelanggan setelah klasterisasi?**  

## Tujuan
1. Melakukan **pembersihan dan pemrosesan data pelanggan** agar siap digunakan dalam analisis segmentasi.
2. Menerapkan **transformasi Yeo-Johnson** dan **standardisasi StandardScaler** untuk memperbaiki distribusi dan skala data RFM.
3. Menentukan jumlah klaster optimal dengan **Elbow Method** dan **Silhouette Score**.
4. Menginterpretasikan hasil klasterisasi dan memberikan wawasan strategis untuk bisnis berdasarkan karakteristik pelanggan dalam setiap klaster.

## Solusi
1. **Pemuatan dan Pemahaman Data**
   - Memuat dataset pelanggan.
   - Memahami struktur data, deskripsi fitur, tipe data, deskripsi statistik.
2. **Penilaian Data**
   - Mengecek missing value, duplikat, dan nilai yang tidak valid.
3. **Pemrosesan Data**
   - Membuat fitur baru
   - Menghitung nilai **RFM**.
   - Menghapus **outlier** menggunakan metode **Interquartile Range (IQR)**.
   - Melakukan **transformasi Yeo-Johnson** untuk memperbaiki distribusi data.
   - Menerapkan **StandardScaler** untuk menstandarkan skala fitur RFM.
5. **Pemodelan dan Evaluasi**
   - Menentukan jumlah klaster optimal menggunakan **Elbow Method**.
   - Menerapkan **K-Means Clustering** dan memberikan label klaster pada pelanggan.
   - Mengevaluasi model menggunakan **Silhouette Score**
6. **Visualisasi dan Interpretasi**
   - Memvisualisasikan hasil clustering dengan **PCA**.
   - Menginterpretasikan setiap klaster berdasarkan nilai rata-rata RFM dari masing-masing kluster untuk memberikan wawasan strategis kepada bisnis.
   
## Pemahaman Data
Data yang digunakan dalam pengembangan model ini adalah data sekunder yang diperoleh dari Kaggle dengan nama dataset "'Online Retail II Data Set from ML Repository", yang dapat diakses melalui tautan berikut: **https://www.kaggle.com/datasets/mathchi/online-retail-ii-data-set-from-ml-repository**. Lebih lanjut, detail mengenai data tersebut diberikan sebagai berikut:

- Data berformat **Excel**.
- Data yang digunakan berasal dari penggabungan data online retail tahun **2009-2010** dan data online retail tahun **2010-2011**.
- Data terdiri dari **1.067.371** sampel dan **8** fitur.

Berikut ini ditampilan 5 sampel awal dari data.

|    |   Invoice | StockCode   | Description                         |   Quantity | InvoiceDate         |   Price |   Customer ID | Country        |
|---:|----------:|:------------|:------------------------------------|-----------:|:--------------------|--------:|--------------:|:---------------|
|  0 |    489434 | 85048       | 15CM CHRISTMAS GLASS BALL 20 LIGHTS |         12 | 2009-12-01 07:45:00 |    6.95 |         13085 | United Kingdom |
|  1 |    489434 | 79323P      | PINK CHERRY LIGHTS                  |         12 | 2009-12-01 07:45:00 |    6.75 |         13085 | United Kingdom |
|  2 |    489434 | 79323W      | WHITE CHERRY LIGHTS                 |         12 | 2009-12-01 07:45:00 |    6.75 |         13085 | United Kingdom |
|  3 |    489434 | 22041       | RECORD FRAME 7" SINGLE SIZE         |         48 | 2009-12-01 07:45:00 |    2.1  |         13085 | United Kingdom |
|  4 |    489434 | 21232       | STRAWBERRY CERAMIC TRINKET BOX      |         24 | 2009-12-01 07:45:00 |    1.25 |         13085 | United Kingdom |

### Fitur Data
Berdasarkan informasi di Kaggle, 7 fitur data pada dataset adalah sebagai berikut:
- **InvoiceNo**: Nomor faktur. Nominal. Merupakan nomor integral 6 digit yang secara - unik diberikan pada setiap transaksi. Jika nomor ini diawali dengan huruf 'C', itu menandakan transaksi tersebut adalah pembatalan (cancellation).
- **StockCode**: Kode produk (item). Nominal. Merupakan nomor integral 5 digit yang secara unik diberikan pada setiap produk yang berbeda.
- **Description**: Nama produk (item). Nominal. Merupakan deskripsi atau nama produk yang dijual dalam transaksi.
- **Quantity**: Jumlah produk (item) per transaksi. Numerik. Menunjukkan jumlah unit dari produk yang dibeli dalam setiap transaksi.
- **InvoiceDate**: Tanggal dan waktu faktur. Numerik. Menunjukkan tanggal dan waktu ketika transaksi dilakukan atau faktur dibuat.
- **UnitPrice**: Harga satuan. Numerik. Merupakan harga per unit produk dalam mata uang sterling (£).
- **CustomerID**: Nomor pelanggan. Nominal. Merupakan nomor integral 5 digit yang secara unik diberikan kepada setiap pelanggan.
- **Country**: Nama negara. Nominal. Menunjukkan nama negara tempat pelanggan tinggal atau berasal.

Ketujuh fitur data tersebut terbagi menjadi:
- **5 fitur kategori** (Invoice, StockCode, Country, Customer ID, Description)
- **3 fitur numerik** merapakan fitur numerik (Quantity, InvoiceDate, Price).

### Deskripsi Statistik 
Berikut adalah hasil pengecekan deskripsi statistik.

|       |         Quantity | InvoiceDate                   |            Price |
|:------|-----------------:|:------------------------------|-----------------:|
| count |      1067371     | 1067371                       |      1067371     |
| mean  |      9.9389      | 2011-01-02 21:13:55.394028544 |      4.64939     |
| min   | -80995           | 2009-12-01 07:45:00           | -53594.4         |
| 25%   |      1           | 2010-07-09 09:46:00           |      1.25        |
| 50%   |      3           | 2010-12-07 15:28:00           |      2.1         |
| 75%   |     10           | 2011-07-22 10:23:00           |      4.15        |
| max   |  80995           | 2011-12-09 12:50:00           |  38970           |
| std   |    172.706       | nan                           |    123.553       |

Interpretasi:

- Quantity (Jumlah Barang yang Dibeli)
    - **Rata-rata jumlah barang yang dibeli per transaksi**: ~9.94 unit.
    - **Rentang quantity sangat luas**: dari **-80,995** hingga **80,995**.
    - **Adanya nilai negatif** mungkin menunjukkan **pembatalan transaksi**.
    - **Standar deviasi cukup besar (172.7)** → menunjukkan variasi tinggi dalam jumlah barang yang dibeli per transaksi.

- InvoiceDate (Tanggal Transaksi)
    - **Rentang transaksi**: dari **1 Desember 2009** hingga **9 Desember 2011**.
    - **Median transaksi terjadi sekitar Desember 2010**, menunjukkan data mencakup lebih dari 2 tahun.

- Price (Harga Barang)
    - **Harga rata-rata per unit**: 4.65.
    - **Ada harga negatif (-53,594.36)**  mungkin mengindikasikan **pengembalian dana (refund)**.
    - **Harga maksimum mencapai $38,970**, menunjukkan ada produk dengan harga sangat mahal.
    - **Standar deviasi cukup tinggi (123.55)** → menunjukkan variasi harga yang signifikan antara produk-produk yang dijual.
      
### Penilaian Data
Pada tahap ini, dilakukan pengecekan terhadap beberapa aspek penting dalam dataset, yaitu:

- **Validitas Data**: Memeriksa apakah nilai data valid atau tidak.
- **Data Duplikat**: Memeriksa apakah terdapat data yang terduplikat atau memiliki nilai yang sama dengan data lainnya.
- **Missing Value**: Memeriksa apakah ada data yang hilang atau tidak tersedia pada fitur-fitur yang ada.

Ditemukan bahwa:
- Terdapat **19.494** transaksi yang dibatalkan (Invoice diawali huruf 'C')
- Terdapat **243.007** Customer ID yang hilang
- Terdapat **4382** Description yang hilang
- Terdapat **22950** Description bernilai negatif dan 5 Price bernilai negatif
- Terdapat StockCode yang terdiri dari 6 digit (kebanyakan berpola 5 digit angka + 1 huruf (kapital/kecil)), yang seharusnya hanya terdiri dari 5 digit angka.

## Pembersihan Data

Langkah selanjutnya adalah melakukan **Pembersihan Data** sebagai berikut:

- **Penghapusan transaksi yang dibatalkan**: Menghapus transaksi yang dibatalkan agar tidak terjadi distorsi data.
- **Penghapusan Customer ID yang hilang**: Menghapus Customer ID yang hilang agar tidak terjadi distorsi data.
- **Penghapusan Description yang hilang**: Menghapus Description yang hilang agar tidak terjadi distorsi data.
- **Penghapusan data dengan Quantity bernilai negatif**: Setelah dilakukan pengecekan lebih lanjut ternyata dari 22.950 sampel dengan Quantity negatif, sebanyak 19.493 sampel berasal dari transaksi yang dibatalkan. Mengingat total transaksi yang dibatalkan berjumlah 19.494, berarti hanya ada satu transaksi yang dibatalkan dengan Quantity tidak negatif. Hal ini menunjukkan bahwa Quantity negatif kemungkinan besar digunakan sebagai penanda transaksi yang dibatalkan. Oleh karena itu, untuk memastikan analisis hanya mencakup transaksi valid, data dengan Quantity negatif perlu dihapus.
- **Penghapusan Price bernilai negatif**: Setalah ditinjau lebih lanjut, transaksi dengan price negatif ternyata tidak merepresentasikan perilaku pelanggan, melainkan penyesuaian keuangan terkait utang buruk ("adjust bad debt"). Karena analisis berfokus pada perilaku pelanggan, transaksi semacam ini sebaiknya dihapus dari dataset untuk menghindari ketidaktepatan dalam hasil analisis.
- **Pertahankan StockKode dengan pola 5 digit angka dan 5 digit angka + 1 huruf kapital**: Setelahan dilakukan analisis lebih lanjut, tambahan angka setelah digit ternyata melambankan variasi dari barang. Namun, terjadi ketidakkonsistenan penulisan huruf setelah 5 digit angka, yang mana itu bisa merupakan huruf kecil atau besar. Setelah di cek lebih lanjut, StockCode dengan pola 5 digit angka + 1 huruf kapital dan 5 digit angka + 1 huruf kecil mempunyai deskripsi yang sama. Oleh karena itu, semua huruf kecil dibelakang 5 digit angka diubah menjadi huruf kapital agar format data lebih konsisten. Dengan demikian StokeCode dengan pola 5 digit angka + 5 digit angka + 1 huruf kapital dipertahankan dalam data.
- **Penghapusan data duplikat**: Menghapus 34.335 sampel yang terduplikat agar tidak terjadi distorsi data.

Setelah dilakukan pembersihan, data terdiri dari 779.425 sampel dan 7 fitur. Berikut ini ditampilan 5 sampel awal dari data yang sudah dibersihkan.

|    |   Invoice | StockCode   | Description                         |   Quantity | InvoiceDate         |   Price |   Customer ID | Country        |   
|---:|----------:|:------------|:------------------------------------|-----------:|:--------------------|--------:|--------------:|:---------------|
|  0 |    489434 | 85048       | 15CM CHRISTMAS GLASS BALL 20 LIGHTS |         12 | 2009-12-01 07:45:00 |    6.95 |         13085 | United Kingdom |   
|  1 |    489434 | 79323P      | PINK CHERRY LIGHTS                  |         12 | 2009-12-01 07:45:00 |    6.75 |         13085 | United Kingdom |     
|  2 |    489434 | 79323W      | WHITE CHERRY LIGHTS                 |         12 | 2009-12-01 07:45:00 |    6.75 |         13085 | United Kingdom |   
|  3 |    489434 | 22041       | RECORD FRAME 7" SINGLE SIZE         |         48 | 2009-12-01 07:45:00 |    2.1  |         13085 | United Kingdom |    
|  4 |    489434 | 21232       | STRAWBERRY CERAMIC TRINKET BOX      |         24 | 2009-12-01 07:45:00 |    1.25 |         13085 | United Kingdom |     

## Pemrosesan Data
### Pembuatan Fitur Baru
Dibuat fitur Revenue dengan cara mengalikan fitur price dan quantity. Fitur Revenue akan digunakan untuk menghitung nilai Monetary dari RFM dengan kolom baru revenue. Berikut ini tampilan data RFM terbaru yang terdiri dari 779.425 sampel dan 8 fitur.

|    |   Invoice | StockCode   | Description                         |   Quantity | InvoiceDate         |   Price |   Customer ID | Country        |   Revenue |
|---:|----------:|:------------|:------------------------------------|-----------:|:--------------------|--------:|--------------:|:---------------|----------:|
|  0 |    489434 | 85048       | 15CM CHRISTMAS GLASS BALL 20 LIGHTS |         12 | 2009-12-01 07:45:00 |    6.95 |         13085 | United Kingdom |      83.4 |
|  1 |    489434 | 79323P      | PINK CHERRY LIGHTS                  |         12 | 2009-12-01 07:45:00 |    6.75 |         13085 | United Kingdom |      81   |
|  2 |    489434 | 79323W      | WHITE CHERRY LIGHTS                 |         12 | 2009-12-01 07:45:00 |    6.75 |         13085 | United Kingdom |      81   |
|  3 |    489434 | 22041       | RECORD FRAME 7" SINGLE SIZE         |         48 | 2009-12-01 07:45:00 |    2.1  |         13085 | United Kingdom |     100.8 |
|  4 |    489434 | 21232       | STRAWBERRY CERAMIC TRINKET BOX      |         24 | 2009-12-01 07:45:00 |    1.25 |         13085 | United Kingdom |      30   |

### Penghitungan RFM
Setelah fitur Revenue ditambahkan, nilai RFM dihitung menggunakan rumus berikut:
- **Recency**  

  R = Tanggal Referensi - Tanggal Transaksi Terakhir
    
- **Frequency (F):** Hitung jumlah transaksi unik yang dilakukan pelanggan.  

  F = Jumlah transaksi unik oleh pelanggan
  
- **Monetary (M):** Hitung total nilai transaksi yang dilakukan pelanggan.  
  
  M = Quantity x Price 

Lebih lanjut nilai RFM dihitung dengan menjalankan kode berikut:

```
# Penentuan Tanggal Referensi (tangal terakhir transaksi + 1 hari)
reference_date = df_cleaned["InvoiceDate"].max() + pd.Timedelta(days=1)
```

```
# Penghitungan Metrik RFM
rfm = df_cleaned.groupby("Customer ID").agg({
    "InvoiceDate": lambda x: (reference_date - x.max()).days,
    "Invoice": "nunique",
    "Revenue": "sum"
}).rename(columns={"InvoiceDate": "Recency", "Invoice": "Frequency", "Revenue": "Monetary"})
rfm.head()
```


Nilai RFM yang sudah dihitung dimuat dalam bentuk data RFM. Data ini terdiri dari 5878 sampel dan 3 fitur (Recency, Frequeny, dan Monetery). Berikut tampilan 5 sampel awal data RFM.

|   Recency |   Frequency |   Monetary |   
|----------:|------------:|-----------:|
|        75 |           5 |    2019.4  |   
|       310 |           1 |     334.4  |      
|       375 |           1 |     300.93 |       
|        36 |          10 |    2849.84 |       
|       204 |           2 |     406.76 |        

### Pengecekan dan Penghapusan Outlier 
Dilakukan pengecekan outlier dari data RFM sebagai berikut.
![16](https://github.com/user-attachments/assets/1df76863-3110-424a-9aad-018a266a77d0)
![17](https://github.com/user-attachments/assets/d56f1bc8-7c47-4f23-97f5-35bb70529a43)
![18](https://github.com/user-attachments/assets/07f1b4c3-0210-4520-8fe5-e23f083c7bba)

Terlihat bahwa terdapat outlier pada fitur Freqency dan Monetery. Oleh karena itu, digunakan metode Inter Quartile Range (IQR) untuk mengatasi outlier. Berikut ini diberikan rumus IQR: 

$IQR = Q_3 - Q_1$

dengan:
*   $IQR$ = Inter Quartile Range
*   $Q_3$ = Quartile 3
*   $Q_1$ = Quartile 1

Setelah dilakukan IQR, data RFM terdiri dari 5001 sampel dan 3 fitur.

### Analisis Univariat
Analisis univariat digunakan untuk memahami karakteristik tipa fitur pada data RFM. Hasil analisis univariat disajikan dalam gambar berikut.

![19](https://github.com/user-attachments/assets/40f80e9d-1d27-4054-9786-9a2a9f7039b3)

Terlihat bahwa ketiga fitur memiliki distribusi yang menceng ke kanan (right-skewed), yang berarti sebagian besar nilai terkonsentrasi di sisi kiri dengan ekor panjang ke kanan. Distribusi seperti ini dapat menyebabkan distorsi dalam analisis data, terutama saat menggunakan metode berbasis jarak seperti K-Means, karena fitur dengan rentang nilai besar akan lebih mendominasi. Oleh karena itu, perlu dilakukan transformasi data untuk membuat distribusi lebih mendekati normal, sehingga model dapat bekerja lebih efektif dan menghasilkan klaster yang lebih akurat.

### Transformasi dan Scaling Data
Selanjutnya, akan dilakukan:
- Tranformasi data menggunakan teknik **Yeo-Johnson**.
- Scaling data menggunaan teknik **StandardScaler**.

Transformasi **Yeo-Johnson** digunakan untuk menstabilkan varians dan membuat data lebih mendekati distribusi normal. Rumusnya adalah sebagai berikut:

$$ T(X; \lambda) =
\begin{cases}
\frac{(X + 1)^\lambda - 1}{\lambda}, & \text{jika } \lambda \neq 0, X \geq 0 \\
\log(X + 1), & \text{jika } \lambda = 0, X \geq 0 \\
-\frac{(-X + 1)^{(2 - \lambda)} - 1}{2 - \lambda}, & \text{jika } \lambda \neq 2, X < 0 \\
-\log(-X + 1), & \text{jika } \lambda = 2, X < 0
\end{cases} $$

dengan:
- **$T(X; \lambda))$** : Hasil transformasi dari data **$X$** dengan parameter **$\lambda$**.  
- **$X$** : Nilai asli dari data yang akan ditransformasikan.  
  - Jika **$X \geq 0$** → mengikuti rumus pertama atau kedua.  
  - Jika **$X < 0$** → mengikuti rumus ketiga atau keempat.  
- **$\lambda$** : Parameter transformasi yang menentukan bentuk kurva perubahan data.  
  - Jika **$\lambda = 0$**, digunakan **log transformation**.  
  - Jika **$\lambda = 2$**, digunakan rumus khusus untuk **$X < 0$**.  
- **$(X + 1)^\lambda $** : Bagian eksponensial yang mengubah skala data untuk **$X \geq 0$**.  
- **$(-X + 1)^{(2 - \lambda)} $** : Bagian eksponensial yang digunakan untuk data negatif **$X < 0$**.  
- **$\log(X + 1) $** dan **$\log(-X + 1)$** : Transformasi logaritmik yang digunakan jika **$\lambda = 0$** atau **$\lambda = 2$**.

Berikut ini adalah tampilan distribusi data RFM terbaru setelah dilakukan transformasi Yeo-Johnson.

![download](https://github.com/user-attachments/assets/b6267f4c-750e-4411-b738-831968864cd9)

Terlihat bahwa kemiringan distribusi Recency dan Frequency berkurang, sementara Monetary mendekati distribusi normal.

Selanjutnya dilakukan scaling menggunakan StandardScaler. StandardScaler mentransformasikan setiap fitur $X$ agar memiliki **mean = 0** dan **standar deviasi = 1** yang membuat semua fitur memiliki skala yang sama, sehingga lebih cocok untuk algoritma berbasis jarak seperti **K-Means**. Rumusannnya diberikan sebagai berikut:


$$X' = \frac{X - \mu}{\sigma}$$

di mana:
- $X'$ = nilai yang sudah diskalakan  
- $X$ = nilai asli fitur  
- $\mu$ = rata-rata fitur  
- $\sigma$ = standar deviasi fitur

Berikut ini deskripsi statistik dari data RFM yang sudah diskalakan.

|       |   Recency |   Frequency |   Monetary |
|:------|----------:|------------:|-----------:|
| count |   5001    |     5001    |    5001    |
| mean  |      0    |       -0    |      -0    |
| std   |      1    |        1    |       1    |
| min   |     -2.02 |       -1.2  |      -3.38 |
| 25%   |     -0.87 |       -1.2  |      -0.73 |
| 50%   |      0.06 |       -0.27 |      -0.02 |
| 75%   |      0.92 |        0.91 |       0.76 |
| max   |      1.6  |        2.01 |       1.99 |

Terlihat semua fitur berada pada rentang nilai -3 hingga 3. Selain itu, juga mempunyai mean 0 dan standar deviasi 1.

## Pemodelan dan Evaluasi
### Penentuan nilai k yang optimal
Sebelum pengelompokan, ditentukan dulu nilai k paling optimal menggunakan elbow method. Elbow method adalah teknik untuk menentukan jumlah klaster optimal dalam K-Means Clustering dengan melihat perubahan nilai inertia atau within-cluster sum of squares (WCSS).

Dalam metode ini, model K-Means dijalankan dengan berbagai nilai k (jumlah klaster), lalu nilai WCSS dihitung untuk setiap k. WCSS mengukur seberapa dekat data dalam satu klaster terhadap pusat klasternya—semakin kecil nilai WCSS, semakin baik klasterisasi.

Hasil WCSS kemudian diplot dalam grafik jumlah klaster (k) vs. WCSS. Titik optimal dipilih berdasarkan bentuk grafik yang menyerupai siku atau "elbow", yaitu titik di mana penurunan WCSS mulai melambat secara signifikan. Titik ini menunjukkan jumlah klaster yang cukup untuk menangkap variasi data tanpa overfitting atau kehilangan pola penting. 

Berikut hasil dari elbow method.

![20](https://github.com/user-attachments/assets/af431b79-a861-4ce7-bfcc-80bb008f17c4)

Grafik di atas menunjukkan hasil **Elbow Method** untuk menentukan jumlah klaster optimal dalam **K-Means Clustering**. Berikut penjelasannya:

#### **Komponen Grafik**
1. **Sumbu X (k)**: Menunjukkan jumlah klaster yang diuji, mulai dari 2 hingga 9.
2. **Sumbu Y kiri (distortion score)**: Nilai **WCSS (Within-Cluster Sum of Squares)** atau distorsi, yang mengukur seberapa dekat data dalam klaster terhadap pusat klasternya.
3. **Sumbu Y kanan (fit time)**: Waktu yang dibutuhkan untuk menjalankan algoritma untuk setiap nilai k.
4. **Garis biru dengan titik berlian**: Menunjukkan perubahan nilai distorsi untuk setiap jumlah klaster.
5. **Garis hijau dengan titik lingkaran**: Menunjukkan waktu komputasi yang dibutuhkan untuk setiap k.
6. **Garis putus-putus vertikal pada k = 4**: Menunjukkan titik siku ("elbow"), yaitu jumlah klaster optimal yang dipilih berdasarkan perubahan drastis dalam nilai WCSS.

#### **Interpretasi**
- Pada **kecilnya nilai k (2-3)**, WCSS masih tinggi karena data belum cukup tersegmentasi dengan baik.
- Pada **k = 4**, terjadi perubahan drastis dalam penurunan WCSS, yang kemudian mulai melambat setelahnya. Ini menandakan bahwa **4 adalah jumlah klaster yang optimal** karena setelah titik ini, penurunan WCSS tidak lagi signifikan.
- Jika kita memilih k lebih dari 4 (misalnya 5 atau 6), nilai WCSS memang lebih kecil, tetapi **penurunannya tidak signifikan dibandingkan sebelumnya**, sehingga menambah klaster lebih banyak tidak memberikan manfaat yang cukup besar.

Berdasarkan **Elbow Method**, jumlah klaster optimal untuk data ini adalah **4 klaster**.

### Klasterisasi dengan K-Means
Dilakukan clusterisaasi dengan K-Means, yaitu metode klasterisasi tidak terawasi (unsupervised learning) yang digunakan untuk mengelompokkan data ke dalam k klaster berdasarkan kesamaan fitur. Dengan mengggunakan **k = 4**, diperoleh silhouette score sebesar **0.384075982836882**, yang berarti: 
- **Klaster memiliki pemisahan yang cukup baik**, tetapi masih ada beberapa data yang berada di dekat batas antar-klaster.  
- **Silhouette Score belum optimal** – meskipun tidak buruk, skor ini bisa lebih baik, mengingat standar umum di mana **Silhouette Score di atas 0.5 dianggap bagus**.  
- **Kemungkinan ada overlap antar klaster**, yang mungkin disebabkan oleh **skala fitur yang kurang tepat** atau **jumlah klaster yang belum optimal**.  

### Visualisasi Cluster dengan PCA
**PCA (Principal Component Analysis)** adalah teknik reduksi dimensi yang digunakan untuk mengubah data berdimensi tinggi menjadi dimensi yang lebih rendah sambil tetap mempertahankan sebanyak mungkin informasi. Dalam konteks klasterisasi, **PCA membantu mempermudah visualisasi data** dengan mereduksi fitur RFM yang memiliki tiga dimensi menjadi dua dimensi utama (*principal components*), sehingga pola antar klaster dapat diamati dengan lebih jelas.

Berikut ini hasil dari PCA. 

![21](https://github.com/user-attachments/assets/a2c74a48-42a9-4d3c-8fa5-f3e6f028f0f9)

Terlihat bahwa cluster terpisah dengan cukup baik.

### Interpretasi Cluster
Dihitung nilai rata-rata Recency, Frequency, Monetery dari masing-masing cluster sebagai berikut.

| Cluster | Recency  | Frequency | Monetary  |
|---------|---------:|----------:|----------:|
| 0       |  44.86  |   6.91    | 2036.99   |
| 1       | 447.03  |   1.19    |  271.36   |
| 2       | 323.70  |   3.68    | 1110.40   |
| 3       |  47.59  |   1.90    |  495.62   |

Berikut ini interpretasi dan strategi bisnis yang bisa diterapkan berdasarkan tabel di atas:

- **Cluster 0: Pelanggan Loyal dengan Pembelanjaan Tinggi**  
  - **Recency (44.86 hari):** Baru saja melakukan pembelian dalam waktu relatif dekat.  
  - **Frequency (6.91 kali):** Sering berbelanja dibandingkan cluster lainnya.  
  - **Monetary (2036.99):** Rata-rata total belanja per pelanggan cukup tinggi.  

**Kesimpulan:**  
Pelanggan di cluster ini adalah pelanggan **loyal** yang sering berbelanja dan mengeluarkan banyak uang. Mereka bisa menjadi **target utama untuk program loyalitas atau penawaran eksklusif**.  



- **Cluster 1: Pelanggan Tidak Aktif dengan Pembelian Rendah**  
  - **Recency (447.03 hari):** Sudah sangat lama tidak melakukan pembelian.  
  - **Frequency (1.19 kali):** Jarang berbelanja.  
  - **Monetary (271.36):** Total belanja per pelanggan paling rendah dibanding cluster lain.  

**Kesimpulan:**  
Cluster ini berisi **pelanggan yang hampir tidak aktif** atau **mantan pelanggan**. Untuk mengaktifkan kembali mereka, strategi seperti **diskon khusus atau email re-engagement** dapat digunakan.  



- **Cluster 2: Pelanggan Berbelanja Secara Berkala dengan Pembelian Sedang**  
  - **Recency (323.70 hari):** Sudah cukup lama sejak terakhir kali melakukan pembelian.  
  - **Frequency (3.68 kali):** Kadang-kadang melakukan pembelian.  
  - **Monetary (1110.40):** Total belanja cukup tinggi.  

**Kesimpulan:**  
Pelanggan di cluster ini masih memiliki **potensi untuk diaktifkan kembali** karena mereka pernah mengeluarkan jumlah uang yang cukup besar, meskipun frekuensi belanjanya tidak tinggi. **Strategi diskon eksklusif atau personalisasi produk bisa efektif untuk menarik mereka kembali.**  



- **Cluster 3: Pelanggan Baru atau Sesekali dengan Pembelian Kecil**  
  - **Recency (47.59 hari):** Baru saja berbelanja dalam waktu relatif dekat.  
  - **Frequency (1.90 kali):** Tidak terlalu sering berbelanja.  
  - **Monetary (495.62):** Pengeluaran tidak terlalu besar.  

**Kesimpulan:**  
Pelanggan di cluster ini bisa jadi adalah **pelanggan baru atau sesekali**. Untuk meningkatkan keterlibatan mereka, bisa diberikan **program welcome reward atau promo yang mendorong mereka untuk kembali berbelanja lebih sering**.


**Kesimpulan Umum & Strategi**  

| **Cluster** | **Karakteristik** | **Strategi Pemasaran** |
|------------|------------------|----------------------|
| **0** | Pelanggan loyal, sering berbelanja, pengeluaran tinggi | Program loyalitas, VIP deals, rekomendasi produk eksklusif |
| **1** | Pelanggan lama yang tidak aktif, pembelian rendah | Email re-engagement, diskon khusus untuk menarik kembali pelanggan |
| **2** | Pelanggan yang pernah aktif dengan pembelanjaan besar | Penawaran personalisasi, promo eksklusif untuk meningkatkan frekuensi pembelian |
| **3** | Pelanggan baru atau sesekali | Welcome rewards, program referral, promo first-time buyer |

Dengan memahami karakteristik masing-masing cluster, dapat diterapkan strategi pemasaran yang lebih efektif dan **memaksimalkan retensi serta nilai pelanggan**.

## Referensi
- Kotler, P., & Keller, K. L. (2015). Marketing Management (15th ed.). Pearson.
- James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). An Introduction to Statistical Learning. Springer.
- Han, J., Kamber, M., & Pei, J. (2011). Data Mining: Concepts and Techniques (3rd ed.). Elsevier.





