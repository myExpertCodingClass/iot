# Sistem Monitoring Pakan Ikan & Kelembapan Berbasis IoT (NodeMCU ESP8266 & Blynk)

Projek ini dirancang untuk memantau kapasitas pakan ikan menggunakan sensor ultrasonik HC-SR04 dan kelembapan sekitar wadah menggunakan sensor DHT11. Data diproses oleh NodeMCU ESP8266 dan dikirimkan secara real-time ke Blynk IoT Platform.

## 📝 Alur Logika Sistem

1. **Inisialisasi**: NodeMCU ESP8266 terhubung ke jaringan WiFi dan melakukan otentikasi ke server Blynk menggunakan Token Akses yang disediakan.
2. **Pembacaan Sensor berkala**: Menggunakan `BlynkTimer`, fungsi `bacaSensor()` dipicu setiap **2 detik sekali** tanpa mengganggu kestabilan koneksi Blynk utama.
3. **Logika Pakan Ikan (Sensor Ultrasonik HC-SR04)**:
   * Sensor memancarkan gelombang ultrasonik untuk mengukur jarak permukaan pakan ikan di dalam wadah.
   * **Jarak $\leq$ 4 cm**: Status **PENUH**, Buzzer MATI.
   * **Jarak $\geq$ 15 cm**: Status **HABIS**, Buzzer MENYALA (pemberitahuan pakan harus diisi).
   * **Jarak di antara 4 cm - 15 cm**: Status **MENIPIS**, Buzzer MATI.
4. **Logika Kelembapan (Sensor DHT11)**:
   * Sensor mendeteksi kelembapan udara sekitar.
   * **Kelembapan > 90%**: Status **BAHAYA**, LED Merah NYALA, LED Kuning MATI.
   * **Kelembapan 60% - 90%**: Status **WASPADA**, LED Kuning NYALA, LED Merah MATI.
   * **Kelembapan < 60%**: Status **AMAN**, semua LED MATI.
5. **Output**: Seluruh data dicetak pada Serial Monitor dan dikirim ke Aplikasi/Web Dashboard Blynk melalui pin virtual `V0` dan `V1`.

---

## 🔌 Tabel Pemetaan Pin & Wiring

Berikut adalah konfigurasi perkabelan antara NodeMCU ESP8266 dengan seluruh komponen sensor dan aktuator:


| Komponen | Pin Komponen | Pin Board (Kode) | Pin Fisik NodeMCU | Fungsi |

| **Sensor HC-SR04** |
| | VCC | Vin / 5V | Vin | Daya Positif Sensor |
| 
| GND | GND | GND | Ground |
| 
| TRIG | 5 | D1 | Trigger Gelombang |
| 
| ECHO | 4 | D2 | Penerima Pantulan Gelombang |

| **Sensor DHT11** |
| | VCC | 3.3V | 3V3 | Daya Positif Sensor |
| | GND | GND | GND | Ground |
| | DATA | 0 | D3 | Transmisi Data Kelembapan |

| **Buzzer** | 
| | Positif (+) | 14 | D5 | Aktuator Alarm Pakan Habis |
| 
| Negatif (-) | GND | GND | Ground |

| **LED Merah** | 
| | Anoda (+) | 15 | D8 | Indikator Kelembapan Bahaya |
| 
| Katoda (-) | GND (via Resistor) | GND | Ground |

| **LED Kuning** | 
| | Anoda (+) | 16 | D0 | Indikator Kelembapan Waspada |
| 
| Katoda (-) | GND (via Resistor) | GND | Ground |

---

## 📊 Konfigurasi Blynk IoT Dashboard

Untuk menampilkan data dari perangkat ke platform Blynk, lakukan langkah-langkah berikut:

### 1. Setup Datastream di Blynk Console
Sebelum membuat widget, Anda wajib mendaftarkan Pin Virtual pada bagian **Datastreams** di Template Blynk:

* **Datastream 1**:
  * Name: Status Pakan
  * Pin: `V0`
  * Data Type: `String`
* **Datastream 2**:
  * Name: Status Kelembapan
  * Pin: `V1`
  * Data Type: `String`

### 2. Setup Web / Mobile Dashboard Widget
Tambahkan komponen visual (Widget) berikut pada bagian Dashboard:

1. **Widget Display Status Pakan**:
   * Jenis Widget: **Label**
   * Hubungkan ke Datastream: `V0` (Status Pakan)
   * Fungsi: Menampilkan teks `PENUH`, `MENIPIS`, atau `HABIS`.

2. **Widget Display Status Kelembapan**:
   * Jenis Widget: **Label**
   * Hubungkan ke Datastream: `V1` (Status Kelembapan)
   * Fungsi: Menampilkan teks `AMAN`, `WASPADA`, atau `BAHAYA`.
