# 🎵 LSTM Music Generator

Proyek ini menggunakan **Long Short-Term Memory (LSTM)** neural network untuk menghasilkan musik secara otomatis berdasarkan pola-pola musik yang dipelajari dari file MIDI.

## 📋 Deskripsi

Music Generator ini menggunakan deep learning untuk mempelajari pola-pola musical dari dataset MIDI, kemudian menghasilkan komposisi musik baru yang memiliki karakteristik serupa dengan data training. Model ini sangat cocok untuk genre seperti lo-fi, chillhop, dan musik ambient.

## ✨ Fitur

- **Deep LSTM Architecture**: Menggunakan 3 layer LSTM dengan batch normalization dan dropout untuk hasil optimal
- **Temperature Sampling**: Kontrol kreativitas dalam generasi musik
- **Automatic Data Processing**: Ekstraksi dan preprocessing otomatis dari file MIDI
- **Training Monitoring**: Early stopping dan learning rate scheduling
- **MIDI Output**: Menghasilkan file MIDI yang dapat diputar di berbagai software musik

## 🛠️ Teknologi

- **Python 3.x**
- **TensorFlow/Keras**: Framework deep learning
- **mido**: Library untuk membaca dan menulis file MIDI
- **scikit-learn**: Preprocessing dan normalisasi data
- **NumPy**: Operasi numerik

## 📦 Instalasi

### Prasyarat

Pastikan Anda memiliki Python 3.x terinstal di sistem Anda.

### Install Dependencies

```bash
pip install mido tensorflow scikit-learn numpy
```

## 🚀 Cara Penggunaan

### 1. Persiapan Data

Siapkan dataset MIDI Anda:
- Buat folder untuk menyimpan file MIDI (contoh: `chillhop/`)
- Masukkan file MIDI ke dalam folder tersebut
- Zip folder tersebut menjadi `chillhopdata.zip`

### 2. Upload Data (untuk Google Colab)

```python
# Upload file zip ke Colab
from google.colab import files
uploaded = files.upload()
```

### 3. Jalankan Pipeline

Notebook ini menggunakan struktur modular dengan 5 langkah utama:

**STEP 1: Verifikasi & Extract File**
- Memverifikasi keberadaan file zip
- Ekstraksi file MIDI
- Pencarian rekursif file MIDI

**STEP 2: Processing MIDI Data**
- Ekstraksi notes dari file MIDI
- Normalisasi data
- Pembuatan sequences untuk training

**STEP 3: Building & Training Model**
- Konstruksi model LSTM
- Training dengan validation
- Model checkpointing

**STEP 4: Generating Music**
- Generasi musik baru dari seed sequence
- Temperature sampling untuk variasi

**STEP 5: Download Files**
- Download model terlatih
- Download file MIDI hasil generasi

### 4. Konfigurasi

Anda dapat menyesuaikan parameter berikut:

```python
SEQUENCE_LENGTH = 50      # Panjang sequence input
EPOCHS = 50               # Jumlah epoch training
BATCH_SIZE = 64           # Ukuran batch
TRAIN_SPLIT = 0.85        # Proporsi data training
```

## 🏗️ Arsitektur Model

Model menggunakan arsitektur LSTM berlapis dengan komponen:

```
Input (50, 1)
    ↓
LSTM Layer 1 (256 units) + BatchNorm + Dropout(0.3)
    ↓
LSTM Layer 2 (256 units) + BatchNorm + Dropout(0.3)
    ↓
LSTM Layer 3 (128 units) + BatchNorm + Dropout(0.3)
    ↓
Dense Layer (64 units, ReLU) + Dropout(0.3)
    ↓
Output Layer (1 unit, Linear)
```

**Total Parameters**: ~997,505 (3.81 MB)

### Loss Function & Optimizer

- **Loss**: Huber Loss (robust terhadap outliers)
- **Optimizer**: Adam (learning rate: 0.001)
- **Metrics**: Mean Absolute Error (MAE)

## 📊 Hasil Training

Dengan dataset 93 file MIDI (4,060 notes):

- **Training Sequences**: 3,408
- **Validation Sequences**: 602
- **Final Validation Loss**: ~0.0083
- **Final Validation MAE**: ~0.0905

Training menggunakan callbacks:
- Early Stopping (patience: 10 epochs)
- Learning Rate Reduction (factor: 0.5, patience: 5)
- Model Checkpoint (menyimpan best model)

## 🎼 Output

Model menghasilkan:

1. **Model File**: `final_music_model.keras` (~12 MB)
2. **Generated MIDI**: `generated_chillhop.mid`
   - 500 notes
   - Durasi: ~250 detik
   - Tempo: 120 BPM (default)

## 🔧 Kustomisasi

### Mengubah Arsitektur Model

```python
model_wrapper.build_model(
    lstm_units=[512, 512, 256],  # Lebih banyak units
    dropout_rate=0.4,             # Dropout lebih tinggi
    learning_rate=0.0005          # Learning rate lebih kecil
)
```

### Mengubah Parameter Generasi

```python
generated = model_wrapper.generate_music(
    seed_sequence=seed_sequence,
    num_notes=1000,        # Lebih banyak notes
    temperature=1.2        # Lebih kreatif (>1) atau konservatif (<1)
)
```

### Mengubah Parameter MIDI Output

```python
MidiGenerator.create_midi(
    notes=generated_notes,
    output_path='my_music.mid',
    tempo=400000,          # Tempo lebih cepat
    velocity=100,          # Volume lebih keras
    duration=240           # Notes lebih pendek
)
```

## 📈 Tips untuk Hasil Optimal

1. **Dataset Quality**: Gunakan file MIDI berkualitas tinggi dengan pola musik yang konsisten
2. **Dataset Size**: Minimal 50-100 file MIDI untuk hasil yang baik
3. **Sequence Length**: 50-100 untuk musik dengan pola kompleks
4. **Training Duration**: Training lebih lama (100-200 epochs) untuk dataset besar
5. **Temperature**: 
   - 0.5-0.8: Musik lebih prediktif dan aman
   - 0.8-1.2: Balance antara kreativitas dan struktur
   - 1.2-2.0: Sangat eksperimental

## 🐛 Troubleshooting

### Error: "Tidak ada file MIDI ditemukan"

**Solusi**: Pastikan struktur folder benar dan file MIDI memiliki ekstensi `.mid` atau `.midi`

### Error: Memory Error

**Solusi**: 
- Kurangi `BATCH_SIZE` (misal: 32 atau 16)
- Kurangi jumlah LSTM units
- Gunakan GPU runtime di Colab

### Generated Music Tidak Bagus

**Solusi**:
- Tambah lebih banyak data training
- Training lebih lama (lebih banyak epochs)
- Adjust temperature parameter
- Gunakan dataset dengan genre musik yang konsisten

## 📝 Struktur Class

### `MusicDataProcessor`
Menangani preprocessing data MIDI:
- `extract_notes_from_midi()`: Ekstrak notes dari files
- `prepare_sequences()`: Buat training sequences
- `inverse_transform()`: Konversi kembali ke skala asli

### `MusicLSTMModel`
Model LSTM untuk music generation:
- `build_model()`: Konstruksi arsitektur
- `train()`: Training dengan callbacks
- `generate_music()`: Generate musik baru
- `save_model()` / `load_model()`: Simpan/load model

### `MidiGenerator`
Konversi notes ke format MIDI:
- `create_midi()`: Buat file MIDI dari list notes

## 📄 Lisensi

Proyek ini bebas digunakan untuk keperluan pembelajaran dan penelitian.

## 🤝 Kontribusi

Kontribusi, saran, dan perbaikan sangat diterima! Silakan buat issue atau pull request.

## 📧 Kontak

Jika ada pertanyaan atau saran, silakan hubungi melalui GitHub issues.

---

**Happy Music Generating! 🎵🎹🎼**
