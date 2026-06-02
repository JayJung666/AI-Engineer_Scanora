# Scanora — AI Engineer Documentation

## 📌 Pengantar 
Dokumentasi ini khusus untuk peran AI Engineer. Fokusnya adalah rekayasa model, custom training loop, inference, serving API, serta monitoring model.

## 🏗️ Artefak Utama
Berikut adalah komponen esensial dalam repositori ini:
- `ai-capstone.ipynb` — Referensi dan *notebook* implementasi pelatihan model.
- `api_main.py` — Layanan inferensi yang dibangun menggunakan **FastAPI**.
- `fruit_model.keras` — Artefak model final yang siap untuk tahap produksi (*production-ready*).
- `logs/` — Direktori penyimpanan log pemantauan dari **TensorBoard**.

## 🎯 Ruang Lingkup & Tanggung Jawab
Sebagai AI Engineer, tugas utama Anda meliputi:
- **Pengembangan Model**: Membangun model *Deep Learning* menggunakan *TensorFlow Functional API* atau *Model Subclassing*.
- **Komponen Kustom**: Telah berhasil mengimplementasikan ketiga komponen lanjutan (*advanced components*) berikut untuk mengoptimalkan performa:
  - **Custom Layer**: *Spatial Attention Layer* (mempertajam fokus fitur).
  - **Custom Loss Function**: *Focal Loss* (menangani ketidakseimbangan data/*class imbalance*).
  - **Custom Callback**: *Early Stopping* kustom (mencegah *overfitting*).
- **Sistem Pelatihan**: Menerapkan *custom training loop* secara penuh berbasis `tf.GradientTape`.
- **Evaluasi & Kualitas**: Memastikan performa model mencapai dan mempertahankan target **akurasi minimum 85%**.
- **Serving Model**: 
  - Menyimpan model akhir dalam format produksi (`.keras` atau `SavedModel`).
  - Mengembangkan REST API mandiri dengan **FastAPI**.
  - Menyediakan alur inferensi yang andal untuk memproses input gambar dari *client*.
- **Observabilitas**: Mengintegrasikan TensorBoard untuk mencatat aktivitas pelatihan dan menyimpan lognya di dalam repositori.

## 📊 Hasil Evaluasi Model
Model yang dikembangkan telah dilatih dan dievaluasi dengan sangat baik. Berikut adalah hasil metrik performa akhir pada tahap pengujian (*testing*):
- **Test Loss**: `0.0083`
- **Test Product Accuracy**: `98.61%` (0.9861)
- **Test Condition Accuracy**: `98.27%` (0.9827)

> **Catatan**: *Performa model ini sangat memuaskan karena telah jauh melampaui target minimum akurasi yang ditetapkan (85%).*

## ⚙️ Standar Teknis & Implementasi

### 1. Arsitektur Model & Pelatihan
- **Basis Model (*Backbone*)**: Menggunakan **EfficientNetV2-B0** (dengan bobot prabentukkan/pre-trained ImageNet) yang sangat andal dan ringan untuk ekstraksi fitur dasar.
- **Komponen Perhatian (*Custom Spatial Attention Layer*)**: Mengaplikasikan *layer* perhatian spasial buatan sendiri setelah *backbone* guna memaksa model berfokus pada area spesifik buah yang menunjukkan ciri kesegaran/kebusukan.
- **Multi-Output (*Heads*)**: Arsitektur dicabangkan di tahap akhir (*Dense layers*) untuk menghasilkan prediksi serentak pada dua label berbeda: kelas **jenis buah** (*product head*) dan **kondisi kesegaran** (*condition head*).
- **Fleksibilitas Pelatihan**: Penggunaan *custom loop* diwajibkan untuk memaksimalkan fleksibilitas kontrol terhadap metrik, *logging*, dan eksperimen lanjutan.
- **Konsistensi Prapemrosesan**: Logika *preprocessing* gambar dan komponen kustom harus **identik** antara fase *training* dan inferensi di API.
- **Iterasi Performa**: Jika performa evaluasi pada data uji dan validasi berada di bawah 85%, segera lakukan penyesuaian pada arsitektur, strategi *fine-tuning*, augmentasi, dan penalaan *hyperparameter*.

### 2. Standar Inferensi API
- **Endpoint**: API wajib menyediakan fitur pengecekan layanan (*health check*) dan pelacakan fungsi prediksi utama.
- **Alur Data**: 
  - API menerima input berupa file gambar dan melakukan validasi format (*validation check*) sebelum memulai inferensi.
  - Memiliki mekanisme perlindungan untuk mendeteksi dan merespons objek yang tidak dikenali (*out-of-distribution*).
- **Output Standard**: Format balasan (respons) inferensi wajib berupa JSON yang berisi metrik berikut:
  - `kelas buah`
  - `kelas kondisi`
  - `confidence score`
  - `freshness score`
  - `saran singkat` (berdasarkan hasil prediksi)
- **Stabilitas Sistem**: API harus menangani akses lintas asal (CORS) agar kompatibel dengan aplikasi klien Web, dan memiliki *error handling* tangguh (untuk kasus model gagal dimuat, *bad input*, atau *inference failure*).
- **Batasan Integrasi AI**: *Endpoint Generative AI* untuk fitur sekunder ditangani langsung di *Front-End/Web*, bukan di repositori AI. Meski begitu, konsistensi format respons (label dan struktur output API) harus dijaga ketat agar selalu sesuai kontrak dengan aplikasi Web.

### 3. Pemantauan (Monitoring)
Seluruh aktivitas log saat siklus pelatihan disimpan dalam folder `logs/` dan dapat divisualisasikan melalui TensorBoard (berfungsi sebagai audit eksperimen). Metrik wajib yang dipantau meliputi:
- *Training loss* & *Validation loss*
- Akurasi *Product head* & Akurasi *Condition head*
- *(Opsional)*: *Learning rate* & *Gradient norm*

## 🚀 Langkah Reproduksi
Ikuti panduan berikut untuk mereplikasi alur pengembangan di *local environment* (sangat disarankan menggunakan sistem dengan GPU yang mendukung CUDA/cuDNN untuk kecepatan pelatihan):
1. **Persiapan Lingkungan**: Instal semua dependensi utama proyek (seperti `tensorflow`, `fastapi`, `uvicorn`, `pandas`, `numpy`, dan `scikit-learn`).
2. **Jalankan Pelatihan**: Eksekusi *pipeline* pelatihan secara utuh menggunakan `ai-capstone.ipynb`.
3. **Verifikasi Kualitas**: Evaluasi *metrics* hingga memenuhi kriteria sukses (akurasi $\ge$ 85%).
4. **Ekspor Model**: Simpan arsitektur & bobot akhir ke dalam artefak siap produksi (`fruit_model.keras`).
5. **Jalankan Layanan API**: Nyalakan layanan peladen (*server*) inferensi menggunakan FastAPI (contoh: `uvicorn api_main:app`).
6. **Pengujian Endpoint**: Lakukan simulasi inferensi dengan beberapa sampel gambar uji dari *client*.
7. **Tinjau Log**: Pastikan log aktivitas dapat diakses dan dianalisis melalui antarmuka TensorBoard.

---
*Terima kasih atas kontribusi dan dedikasi Anda. Mari wujudkan infrastruktur kecerdasan buatan Scanora yang optimal dan berkelanjutan!*
