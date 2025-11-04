1️⃣ Console Message Analysis

Setelah login dan membuka menu **Setorku → Bulk Upload**, dilakukan inspeksi melalui browser console (tab *Console* pada Developer Tools).

**Hasil observasi:**
- Tidak terdapat error JavaScript atau network error pada saat page load.  
- Pesan console hanya menampilkan log internal framework (Next.js dan React rendering info).  
- Semua request berhasil dijalankan dengan status code **200 (OK)**.  

 2️⃣ API Endpoint, Headers & Parameters

Selama proses eksplorasi fitur dan klik pada semua tombol halaman, berikut hasil analisis API endpoint dan parameter yang terekam melalui tab *Network*.

▶️ Endpoint: Get Bulk Upload Data
```
GET https://stg-sob.ids.id/api/main/setorku/bulk-upload
```

Headers:
| Header | Value |
|--------|--------|
| Content-Type | application/json; charset=utf-8 |
| Accept | application/json, text/plain, */* |
| Cookie | __Secure-next-auth.session-token=... |
| Referrer Policy | strict-origin-when-cross-origin |

Query Parameters:
| Parameter | Contoh Nilai | Keterangan |
|------------|--------------|------------|
| fromDate | 03-10-2025 | Filter tanggal awal |
| toDate | 03-11-2025 | Filter tanggal akhir |
| pageNumber | 1 | Nomor halaman data |
| pageSize | 10 | Jumlah data per halaman |
| branchId | 5010008 | Filter cabang |
| partnerId | OTO | Filter partner |
| status | 0 | Filter status (Pending/Approved/Rejected) |

---

▶️ Endpoint: Add Data
```
POST https://stg-sob.ids.id/api/main/setorku/bulk-upload/add
```

Body (JSON):
```json
{
  "Name": "Test Data",
  "MerchantTransactionID": "TRX20251103",
  "UserCode": "USR001",
  "UserName": "QA Tester",
  "CustomerID": "CST2025",
  "Amount": 500000,
  "Periode": "10"
}
```

Expected Response:
- **Status:** 200 OK  
- **Message:** “Data successfully added.”  

---

▶️ Endpoint: Approve Data
```
POST https://stg-sob.ids.id/api/main/setorku/bulk-upload/approve
```

Expected Result:  
- Status berubah menjadi *Approved*  
- User mendapatkan *Kode Bayar*  
- Response 200 OK  

---

▶️ Endpoint: Reject Data
```
POST https://stg-sob.ids.id/api/main/setorku/bulk-upload/reject
```

Expected Result:
- Data berubah status menjadi *Rejected*  
- Response 200 OK  

---

3️⃣ Test Case Documentation

Berikut hasil penyusunan test case berdasarkan fungsi yang tersedia di halaman **Bulk Upload**.

| TC ID | Test Scenario | Steps | Expected Result |
|-------|----------------|-------|-----------------|
| **TC_BU_001** | View default data | 1. Login as `ryobranch`  2. Buka menu *Setorku → Bulk Upload* | Data tampil di tabel sesuai default filter. Tidak ada error di console. |
| **TC_BU_002** | Filter by Date | 1. Isi tanggal awal & akhir  2. Klik **Search** | API mengirim parameter `fromDate` & `toDate`, data tampil sesuai range. |
| **TC_BU_003** | Filter by Partner | 1. Pilih partner “OTO”  2. Klik **Search** | API mengirim `partnerId=OTO`, data sesuai partner. |
| **TC_BU_004** | Filter by Branch | 1. Pilih cabang “5010008”  2. Klik **Search** | API mengirim `branchId=5010008`, data hanya dari cabang tersebut. |
| **TC_BU_005** | Filter by Status | 1. Pilih status “Pending”  2. Klik **Search** | API mengirim `status=0`, data pending tampil. |
| **TC_BU_006** | Pagination | 1. Klik “Next Page” di tabel | API mengirim `pageNumber=2`, data berubah sesuai halaman. |
| **TC_BU_007** | Add New Data | 1. Klik tombol **Add**  2. Isi semua mandatory field  3. Klik **Submit** | Data tersimpan, muncul di tabel, dan validasi berjalan sesuai aturan karakter. |
| **TC_BU_008** | Approve Data | 1. Pilih 1 data pending  2. Klik **Approve** | Status berubah ke *Approved*, muncul *Kode Bayar*. |
| **TC_BU_009** | Reject Data | 1. Pilih 1 data pending  2. Klik **Reject** | Status berubah ke *Rejected*, data tidak muncul di list pending. |
| **TC_BU_010** | Field Validation | 1. Kosongkan mandatory field  2. Klik Submit | Muncul pesan error “Field is required” / “Invalid format”. Tidak ada crash. |

---
