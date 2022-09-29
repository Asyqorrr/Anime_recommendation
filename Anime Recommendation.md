# Project Overview
Anime adalah animasi buatan Jepang, yang kini berkembang menjadi bentuk budaya populer, dengan basis penggemar yang cukup banyak. Anime tidak hanya dipandang sebagai sarana hiburan semata, sebagian besar orang bahkan menganggap kartun tersebut sebagai tontonan hari-hari, lantaran cerita kerap menginspirasi.  

Dengan banyaknya penggemar anime di seluruh dunia dan banyaknya genre-genre anime yang tersebar, maka model AI yang dapat merekomendasi anime yang disukai oleh penonton berdasarkan judul anime yang sama akan sangat membantu penggememar anime untuk mencari anime yang mungkin akan disukai olehnya.

Banyaknya judul anime mungkin akan membuat beberapa penonton baru merasa sedikit kewalahan karena penonton baru akan bingung dalam memilih anime apa yang cocok dengan selera penonton itu sendiri.


# Business Understanding

## Problem Statement
Berdasarkan kondisi yang sudah disampaikan pada project overview, maka penulis mengembangkan sistem rekomendasi anime untuk menjawab permasalahan tersebut.

 - Dari permasalahan diatas ada genre apa saja yang terdapat di anime?
 - Adakah kemiripan dari tiap anime berdasarkan genre tersebut?

## Goals
 - Mengetahui genre apa saja yang terdapat didalam judul anime
 - Mendapatkan rekomendasi kemiripan antar anime berdasarkan genrenya

## Metrik
Untuk sistem rekomendasi anime ini yang menggunakan teknik Content Based Filtering saya menggunakan metriks Jaccard Similarity Coefficient yang menghitung seberapa dekat jarak antara dua set sampel.
Jaccard distance di definisikan sebagai berikut 

**gambar rumus jaccard distance

# Data Understanding
## Dataset Information
Dataset yang saya gunakan adalah dataset Anime Recommendations Database yang di upload oleh cooperunion.

https://www.kaggle.com/datasets/CooperUnion/anime-recommendations-database

Memiliki 2 File yaitu :

Anime.csv yang memiliki 7 kolom:

 - anime_id 
 - name 
 - genre 
 - type 
 - episodes 
 - rating 
 - members

Rating,csv memiliki 3 kolom:

 - user_id 
 - anime_id 
 - rating 

dataset ini mempunyai informasi tentang data preferensi pengguna dari 73.516 pengguna dan 12.294 anime.

## Data Loading
Dengan menggunakan google collab dan API dari kaggle saya mengunduh file dataset tersebut langsung dari google collab ke kaggle.

## Explarotary Data Analysis
### Variable Description
Memiliki 2 File yaitu :

Anime.csv yang memiliki 8 kolom:
 - anime_id = id dari tiap judul anime
 - name = judul anime
 - genre = genre dari tiap judul anime
 - type = tipe anime seperti apakah bertipe series atau movie
 - episodes = jumlah episode anime
 - rating = total rata-rata keseluruhan rating tiap judul anime
 - members = banyak komunitas yang berada di anime 'group'

Rating,csv memiliki 3 kolom:
 - user_id = id dari user yang memberikan rating
 - anime_id = id anime yang diberikan rating oleh user
 - rating = nilai rating yang diberikan user ke anime
 
### Data Condition
isi dari file :
#### anime.csv 
 - anime_id = id anime bertipe integer (12294 data)
 - name = judul anime bertipe string (12294 data)
 - genre = string (12232 data) yang memiliki variasi genre seperti ['Drama, Romance, School, Supernatural' 'Action, Adventure, Drama, Fantasy, Magic, Military, Shounen' 'Action, Comedy, Historical, Parody, Samurai, Sci-Fi, Shounen' ... 'Hentai, Sports' 'Drama, Romance, School, Yuri' 'Hentai, Slice of Life']
 
 - type = string (12269 data) yang memiliki variasi tipe seperti ['Movie' 'TV' 'OVA' 'Special' 'Music' 'ONA' nan]
- episodes = string (12294 data) bervariasi sebanyak 187 variasi episode
- rating = float (12064 data) dengan rentang nilai 1.67 - 10.0
- member = string (12294 data) bervariasi sebanyak 6706

#### rating.csv
 - user_id = id user bertipe integer 
 - anime_id = id anime bertipe integer
 - rating = rating anime bertipe integer

# Data Preparation
## Dataframe anime
### Menghapus data null
Pertama dataframe ini di cek apakah ada nilai null, setelah di cek ternyata ada nilai null menggunakan fungsi isnull().sum(), lalu saya menghapus nilai null yang terdapat pada dataframe ini dengan fungsi dropna()
### Menghapus kolom yang tidak diperlukan
Pada teknik content based filtering kolom yang dibutuhkan pada dataset ini hanyalah anime_id, name, dan genre

## Dataframe rating
### Menghapus value data yang invalid pada dataframe
Pada kolom rating di dataframe rating terdapat nilai -1 yang berarti user sudah menonton anime namun tidak memberikan rating, untuk kasus ini penulis akan menghapus setiap rating bernilai -1 dengan cara merubah nilai -1 dengan fungsi replace(-1, np.nan) menjadi null lalu menghapus nilai null dengan dropna().

## Menggabungkan dataframe 
Tahap selanjutnya adalah menggabungkan dataframe anime dan rating berdasarkan kolom yang sama dan merubah ke bentuk numpy array yaitu anime_id menggunakan fungsi np.concatenate().

Setelah itu dilakukan pengurutan data yang tadi digabungkan dan menghapus data yang terduplikat menggunakan fungsi numpy np.sort(np.unique)

## Melihat informasi dataframe 
Melihat seluruh data anime berdasarkan anime_id menggunakan len(anime_all).

Melihat berapa banyak user yang memberikan rating menggunakan variabel user_all yang diambil dari data user_id lalu diurutkan dan dirubah ke bentuk numpy array menggunakan np.sort(np.unique()) dan dihitung panjang array tersebut dengan len(user_all).

## Mengetahui jumlah rating
### Menggabungkan dataframe anime dan rating
Menggabungkan dataframe anime dan rating berdasarkan anime_id dengan fungsi pd.merge lalu di cek missing valuenya dengan isnull().

## Menggabungkan data dengan fitur judul anime
Menggabungkan dataframe rating yang sudah dirubah variabelnya menjadi all_anime_rate dengan kolom dataframe anime, anime_id dan name pada variabel all_anime_name.

## Menggabungkan data dengan fitur genre anime
Menggabungkan dataframe all_anime_name dengan kolom dataframe anime, anime_id dan genre pada variabel all_anime
Sehingga kolom yang terdapat pada dataframe all_anime ada user_id, anime_id, rating, name, dan genre.

## Mengatasi missing value dataframe all_anime
mengecek missing value dataframe all_anime, lalu menghapus missing value yang terdapat pada dataframe tersebut.

## Konversi data ke list 
mengkonversi kolom anime_id, name, dan genre ke bentuk list dari dataframe all_anime

## Buat dataframe dengan dictionary 
Membuat dataframe baru bernama anime_dict dengan dictionary (id : anime_id, anime_name : anime_name, genre : genre)

# Modelling
## TF-IDF Vectorizer
Teknik ini digunakan untuk merepresentasikan fitur penting dari setiap genre.
kolom genre akan di fit dan transform dengan TfidfVectorizer() pada variabel tfidf_matrix karena kita akan merekomendasikan anime berdasarkan genrenya.
TF-IDF Vectorizer menghasilkan matriks yang menunjukkan korelasi antara judul anime dengan genrenya.

## Cosine Similarity
Tahap selanjutnya adalah menggunakan cosine similarity untuk menghitung kesamaan/kemiripan antar judul anime.
variabel tfidf_matrix akan dimasukkan ke fungsi cosine_similarity sehingga dapat dibuat dataframe yang berisi informasi korelasi antara anime satu dengan yang lainnya.

## Mendapatkan Rekomendasi
Menggunakan fungsi anime_recommendation kita dapat menggunakan fungsi tersebut untuk mendapatkan rekomendasi anime berdasarkan judul anime yang di input.
Banyaknya rekomendasi tergantung dari nilai k yang di define di fungsi anime_recommendation, pada kasus ini k = 5.

## Hasil Rekomendasi 
Hasil dari rekomendasi judul anime yang di input dengan judul 'Bleach' adalah sebagai berikut

**gambar rekom anime

# Evaluation
## Jaccard Similarity Coefficient
Menghitung presisi model dengan jaccard similarity coefficient yang menghitung irisan antara sampel yaitu judul anime yang mirip dengan keseluruhan sampel.
Berarti berdasarkan input judul anime kita maka JSC akan mengecek berapa banyak judul anime yang memiliki kemiripan dengan input judul anime sebelumnya.

Hasil dari metrik menggunakan jaccard similarity coefficient dengan input judul anime 'Bleach' adalah 0.85

**ss jaccard_value_total
