# Cashflowmation

Cashflowmation adalah workflow n8n untuk mengekstrak transaksi pendapatan dan pengeluaran dari email, menyimpannya ke Google Sheets, serta mengirimkan notifikasi persetujuan dan laporan ke Telegram untuk membantu pengelolaan arus kas UMKM.

![Tangkapan layar Cashflowmation](Component/Cashflowmation_screenshot.png)

## Fitur Utama

- Mengambil maksimal tiga email yang cocok dengan subjek `invoice`, `faktur`, `pembayaran`, `receipt`, atau `tagihan`.
- Mengekstrak tanggal, tipe transaksi, status, pihak terkait, deskripsi, nomor invoice, nominal, tanggal jatuh tempo, dan ID email menggunakan model bahasa.
- Memisahkan transaksi ke sheet `INCOME` dan `EXPENSE` di Google Sheets.
- Meminta persetujuan melalui Telegram untuk pengeluaran yang belum dibayar, lalu memperbarui status menjadi `paid` atau `rejected`.
- Mengirim laporan pendapatan, pengeluaran, dan laba atau rugi bersih harian melalui Telegram.

## Prasyarat

- Instance n8n yang dapat mengimpor dan menjalankan workflow JSON. Versi n8n yang kompatibel tidak ditentukan di repositori: `[ISI_DI_SINI]`.
- Akun Gmail dengan akses OAuth2.
- Akun Google dan spreadsheet dengan sheet `INCOME` serta `EXPENSE`.
- Akun Telegram dan bot Telegram.
- Akses model Google Gemini dan Groq yang dikonfigurasi pada node workflow.
- Akses ke repository ini atau salinan file `Cashflowmation.json`.

## Instalasi

1. Salin atau clone repository ini.
2. Buka instance n8n.
3. Import file `Cashflowmation.json` ke n8n.
4. Hubungkan credential Gmail OAuth2, Google Sheets, Telegram, Google Gemini, dan Groq pada node yang sesuai.
5. Pastikan spreadsheet memiliki sheet `INCOME` dan `EXPENSE` dengan kolom yang digunakan workflow.
6. Simpan workflow, lakukan pengujian dengan email transaksi, lalu aktifkan workflow.

Repositori ini tidak memiliki `package.json`, `requirements.txt`, Dockerfile, atau script instalasi dan pengujian. Karena itu, tidak ada perintah terminal khusus proyek yang perlu dijalankan.

## Cara Penggunaan

### Pemrosesan Email Transaksi

Workflow menjalankan pemicu terjadwal dengan interval jam yang dikonfigurasi pada node `email_trigger`. Workflow mengambil email yang sesuai filter, memprosesnya melalui model bahasa, kemudian mengarahkan data ke alur pendapatan atau pengeluaran.

Alur utamanya adalah:

1. Gmail mengambil email dengan kata kunci transaksi.
2. Model bahasa menghasilkan objek JSON terstruktur.
3. Node `parsing` membaca JSON dan node `switch` memilih `INCOME` atau `EXPENSE`.
4. Data ditambahkan ke Google Sheets.
5. Pengeluaran belum dibayar dikirim ke Telegram untuk persetujuan.

### Laporan Harian

Node `daily_trigger` dijadwalkan pada pukul 20:00. Workflow membaca data dari kedua sheet, menjumlahkan pendapatan dan pengeluaran, menghitung laba atau rugi bersih, lalu mengirimkan ringkasannya melalui Telegram.

### Format Data Hasil Ekstraksi

Model bahasa diharapkan mengembalikan JSON tanpa teks tambahan dengan bentuk berikut:

```json
{
    "date": "YYYY-MM-DD",
    "type": "income | expense",
    "status": "paid | unpaid",
    "sender": "string",
    "description": "string",
    "invoice_number": "string",
    "amount": "number",
    "due_date": "YYYY-MM-DD",
    "email_id": "string"
}
```

## Konfigurasi

### Credential dan Layanan

Workflow menggunakan credential n8n untuk Gmail OAuth2, Google Sheets, Telegram Bot API, Google Gemini, dan Groq. Credential tersebut harus dikonfigurasi melalui n8n; tidak ada file `.env.example` atau daftar environment variable di repositori.

### Google Sheets

Workflow menggunakan satu spreadsheet dengan dua sheet berikut:

| Sheet     | Kolom utama                                                                                     |
| :-------- | :---------------------------------------------------------------------------------------------- |
| `INCOME`  | `date`, `status`, `customer`, `description`, `invoice_number`, `amount`, `email_id`             |
| `EXPENSE` | `date`, `status`, `supplier`, `description`, `invoice_number`, `amount`, `due_date`, `email_id` |

ID spreadsheet dan pemetaan kolom tersimpan di node Google Sheets dalam file workflow. Verifikasi bahwa referensi tersebut menunjuk ke spreadsheet milik Anda sebelum workflow diaktifkan.

### Model Bahasa

Workflow memiliki node Google Gemini dan Groq. Model yang tercantum pada node Groq adalah `openai/gpt-oss-120b`; versi model Gemini tidak ditentukan di README maupun metadata proyek dan perlu diverifikasi di instance n8n.

## Struktur Folder

```text
Cashflowmation/
├── Cashflowmation.json
├── Cashflowmation_presentation.pptx
├── Component/
│   ├── Cashflowmation_screenshot.png
│   ├── image.png
│   ├── image-1.png
│   └── image-2.png
└── README.md
```

## Latar Belakang dan Solusi

Pemilik UMKM yang mengelola operasional tanpa admin keuangan berisiko melewatkan pencatatan invoice, pembayaran, dan tagihan. Workflow ini mengurangi pencatatan manual dengan mengekstrak informasi transaksi dari email, menyimpannya secara terstruktur di Google Sheets, dan menyediakan notifikasi Telegram.

## Technology Stack

| Teknologi              | Peran                                          |
| :--------------------- | :--------------------------------------------- |
| n8n                    | Orkestrasi workflow dan integrasi layanan      |
| Gmail                  | Sumber email transaksi                         |
| Google Gemini dan Groq | Ekstraksi data transaksi berbasis model bahasa |
| Google Sheets          | Penyimpanan ledger pendapatan dan pengeluaran  |
| Telegram Bot           | Persetujuan pengeluaran dan laporan harian     |

## Output

### Ledger Google Sheets

![Contoh data pendapatan](Component/image.png)

![Contoh data pengeluaran](Component/image-1.png)

### Laporan Telegram

![Contoh laporan Telegram](Component/image-2.png)

## Informasi Repository

Workflow n8n tersedia pada [Cashflowmation.json](Cashflowmation.json). 

Workflow n8n yang digunakan tersedia di [tautan instance n8n](https://raliefr.app.n8n.cloud/workflow/HSHx17e77Pw0QsAg).
