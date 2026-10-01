# 🚀 AutoCashflow n8n: Automated Cashflow Tracking System for Solo Business Owners

![n8n](https://img.shields.io/badge/n8n-Workflow%20Automation-FF6D5A?style=for-the-badge&logo=n8n&logoColor=white)
![GeminiAI](https://img.shields.io/badge/GeminiAI-GPT--4o--mini-10A37F?style=for-the-badge&logo=GeminiAI&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Database-0F9D58?style=for-the-badge&logo=googlesheets&logoColor=white)
![Telegram](https://img.shields.io/badge/Telegram-Bot%20Notifications-229ED9?style=for-the-badge&logo=telegram&logoColor=white)

An automated cashflow management system built with **n8n** and **GeminiAI (LLM)** designed specifically for solo MSME (UMKM) owners. This workflow automatically extracts incoming income and expense data directly from transaction emails and logs them into a Google Sheet ledger while sending instant real-time alerts via Telegram.

---

## 📌 Problem Background

Solo business owners often struggle to keep track of daily income and expenses due to operational bandwidth limits:
- ⏳ **Time-Consuming Manual Entry:** Copying invoice data, transfer receipts, and notes into spreadsheets takes hours away from business growth.
- 📬 **Scattered Transaction Records:** E-invoices and payment confirmation emails pile up without centralized aggregation.
- ⚠️ **Financial Risk & Leakage:** Delayed reporting leads to an unclear view of real-time balance positions.

### 💡 The Solution
**AutoCashflow n8n** automates **85% of administrative work** by capturing incoming transaction emails, utilizing AI to parse details into structured JSON, and updating financial ledgers instantly.

---

## 🛠️ Tech Stack & Integration

| Tool / Platform | Role in System |
| :--- | :--- |
| **n8n** | Primary workflow orchestrator managing email polling, AI API calls, and logic flow. |
| **GeminiAI Model** | Natural Language Processing (LLM) to extract structured financial data from raw email bodies. |
| **Google Sheets** | Cloud database acting as an automated ledger for real-time reporting. |
| **Telegram Bot** | Instant notification channel delivering instant cashflow transaction summaries to the owner's mobile device. |

---

## 🔄 Workflow Architecture & Reasoning

```
[ Gmail Trigger ] ➡️ [ GeminiAI Extraction ] ➡️️ [ Google Sheets Sync ] ➡️ [ Telegram Alert ]
```

1. **Gmail Trigger:** Polls incoming emails every 5 minutes with targeted filter keywords (`Invoice`, `Pembayaran`, `Transfer`, `Nota`).
2. **AI Data Extraction:** The email body is sent to GeminiAI to extract essential transactional attributes into structured JSON.
3. **Spreadsheet Sync:** Parsed transaction data is written as a new row into Google Sheets.
4. **Telegram Alert:** Sends a real-time message confirmation to the owner's Telegram app.

---

## 🧠 AI Prompt Design

The system relies on a strictly typed System Prompt to ensure predictable JSON parsing without markdown wrappers:

```json
// System Prompt
Kamu adalah asisten keuangan UMKM.
Tugas: Ekstrak data transaksi dari body email berikut ke JSON murni tanpa markdown formatting.

{
  "tanggal": "YYYY-MM-DD",
  "tipe": "pemasukan | pengeluaran",
  "nominal": 0,
  "kategori": "string",
  "keterangan": "string"
}
```

### Key Prompt Design Considerations:
- **Strict JSON Constraint:** Prevents conversational conversational boilerplate from breaking n8n node parsing.
- **Explicit Type Classification:** Segregates incoming money (*pemasukan*) from outgoing expenses (*pengeluaran*).
- **Number Normalization:** Standardizes formatted currency strings (e.g., `"Rp 1.500.000"`) into pure integer values (`1500000`).

---

## 📸 Input & Output Visuals

### 1. Trigger Input (Email Notification)
Raw transaction emails received from suppliers or payment gateways:
> **Subject:** Laporan Transaksi Masuk #INV-8821  
> **Body:** Halo Owner UMKM, Pembayaran untuk pembelian **Bahan Baku Kopi 10kg** sebesar **Rp 1.500.000** telah berhasil diverifikasi pada tanggal **02 Oktober 2026**.

### 2. Final Output (Google Sheets Ledger & Telegram Bot)
**Google Sheets Entry:**
| Tanggal | Tipe | Nominal | Kategori | Keterangan |
| :--- | :--- | :--- | :--- | :--- |
| `2026-10-02` | `Pengeluaran` | `Rp 1.500.000` | `Bahan Baku` | `Supplier Jaya` |

**Telegram Bot Alert:**
```text
🟢 Transaksi Baru Dicatat!
• Tipe: Pengeluaran
• Nominal: Rp 1.500.000
• Kategori: Bahan Baku
• Tanggal: 02 Okt 2026
```

---

## ⚙️ How to Deploy / Import

1. **Prerequisites:**
   - Active **n8n** instance (Self-hosted or Cloud).
   - **GeminiAI API Key**.
   - **Google Sheets API** credentials enabled in Google Cloud Console.
   - **Telegram Bot Token** created via `@BotFather`.

2. **Import Workflow:**
   - Copy the `cashflowmation.json` file from this repository.
   - Open n8n, click **Import from File / URL**, and paste the JSON.
   - Configure your credentials for Gmail, GeminiAI, Google Sheets, and Telegram.
   - Activate the workflow!

---
*Created as part of the n8n Workflow Automation Project.*