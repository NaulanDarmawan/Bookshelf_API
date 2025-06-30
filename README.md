# Bookshelf API

## Deskripsi

Bookshelf API adalah sebuah layanan RESTful API yang dibangun menggunakan Node.js dan Hapi Framework. API ini menyediakan fungsionalitas dasar untuk mengelola data koleksi buku, seperti menambah, menampilkan, mengubah, dan menghapus buku. Proyek ini menggunakan _in-memory array_ sebagai basis data, yang berarti semua data akan di-reset setiap kali server dihentikan.

Proyek ini dirancang untuk menunjukkan kemampuan dalam membangun backend API yang terstruktur dan memenuhi kriteria RESTful.

## Fitur

* **Create**: Menambahkan data buku baru ke dalam rak.
* **Read**:
    * Menampilkan seluruh daftar buku dengan data ringkas (`id`, `name`, `publisher`).
    * Mendukung *query parameter* untuk memfilter buku berdasarkan `name`, status `reading`, dan status `finished`.
    * Menampilkan informasi detail dari satu buku berdasarkan ID-nya.
* **Update**: Memperbarui data buku yang sudah ada berdasarkan ID.
* **Delete**: Menghapus data buku dari rak berdasarkan ID.

## Teknologi yang Digunakan

* **Node.js**: Lingkungan eksekusi JavaScript di sisi server.
* **Hapi**: Framework Node.js yang *rich* dan terkonfigurasi untuk membangun aplikasi dan layanan API.
* **nanoid**: Library untuk menghasilkan ID unik yang aman dan ringkas untuk setiap buku.
* **nodemon**: Utilitas untuk *development* yang secara otomatis me-restart server setiap ada perubahan pada kode.
* **ESLint**: Alat untuk menjaga konsistensi dan kualitas kode.

## Instalasi & Konfigurasi

Untuk menjalankan proyek ini secara lokal, ikuti langkah-langkah di bawah ini:

1.  **Clone repositori ini:**
    ```bash
    git clone [https://github.com/NaulanDarmawan/Bookshelf_API.git](https://github.com/NaulanDarmawan/Bookshelf_API.git)
    ```

2.  **Masuk ke direktori proyek:**
    ```bash
    cd Bookshelf_API
    ```

3.  **Install semua *dependency* yang dibutuhkan:**
    ```bash
    npm install
    ```

4.  **Jalankan server:**
    * Untuk mode *development* (dengan auto-reload saat ada perubahan kode):
        ```bash
        npm run start-dev
        ```
    * Untuk mode *production*:
        ```bash
        npm run start
        ```

5.  Server akan berjalan pada `http://localhost:9000`.

## Dokumentasi API

Berikut adalah daftar *endpoint* yang tersedia beserta detail penggunaannya.

---

### **1. Menambahkan Buku Baru**

Menyimpan objek buku baru ke dalam array `books`.

* **Endpoint**: `/books`
* **Method**: `POST`
* **Body Request** (`application/json`):
    ```json
    {
        "name": "Buku A",
        "year": 2020,
        "author": "John Doe",
        "summary": "Lorem ipsum dolor sit amet",
        "publisher": "Dicoding Indonesia",
        "pageCount": 100,
        "readPage": 25,
        "reading": false
    }
    ```
* **Validasi**:
    * Properti `name` wajib diisi. Jika tidak, respons akan `400 Bad Request` dengan pesan "Gagal menambahkan buku. Mohon isi nama buku".
    * Nilai `readPage` tidak boleh lebih besar dari `pageCount`. Jika lebih besar, respons akan `400 Bad Request` dengan pesan "Gagal menambahkan buku. readPage tidak boleh lebih besar dari pageCount".
* **Respon Sukses** (`201 Created`):
    ```json
    {
        "status": "success",
        "message": "Buku berhasil ditambahkan",
        "data": {
            "bookId": "a1b2c3d4e5f6g7h8"
        }
    }
    ```
* **Respon Gagal Lainnya** (`500 Internal Server Error`): Terjadi jika buku gagal dimasukkan ke array karena alasan yang tidak terduga.

---

### **2. Mendapatkan Semua Buku**

Mengambil daftar buku. Mendukung penyaringan melalui *query parameter*.

* **Endpoint**: `/books`
* **Method**: `GET`
* **Query Parameters** (Opsional):
    * `name` (string): Tampilkan buku yang namanya mengandung string ini (pencarian *case-insensitive*).
        * Contoh: `GET /books?name=dicoding`
    * `reading` (number): Filter berdasarkan status dibaca. `1` untuk `true`, `0` untuk `false`.
        * Contoh: `GET /books?reading=1`
    * `finished` (number): Filter berdasarkan status selesai dibaca. `1` untuk `true`, `0` untuk `false`.
        * Contoh: `GET /books?finished=0`
* **Respon Sukses** (`200 OK`):
    ```json
    {
        "status": "success",
        "data": {
            "books": [
                {
                    "id": "a1b2c3d4e5f6g7h8",
                    "name": "Buku A",
                    "publisher": "Dicoding Indonesia"
                },
                {
                    "id": "i9j0k1l2m3n4o5p6",
                    "name": "Buku B",
                    "publisher": "Penerbit Y"
                }
            ]
        }
    }
    ```

---

### **3. Mendapatkan Detail Buku**

Mengambil informasi lengkap sebuah buku berdasarkan ID.

* **Endpoint**: `/books/{bookId}`
* **Method**: `GET`
* **Respon Sukses** (`200 OK`):
    ```json
    {
        "status": "success",
        "data": {
            "book": {
                "id": "a1b2c3d4e5f6g7h8",
                "name": "Buku A",
                "year": 2020,
                "author": "John Doe",
                "summary": "Lorem ipsum dolor sit amet",
                "publisher": "Dicoding Indonesia",
                "pageCount": 100,
                "readPage": 25,
                "finished": false,
                "reading": false,
                "insertedAt": "2024-06-30T12:00:00.000Z",
                "updatedAt": "2024-06-30T12:00:00.000Z"
            }
        }
    }
    ```
* **Respon Gagal** (`404 Not Found`): Terjadi jika `bookId` tidak ditemukan.
    ```json
    {
        "status": "fail",
        "message": "Buku tidak ditemukan"
    }
    ```

---

### **4. Mengubah Data Buku**

Memperbarui data buku yang sudah ada berdasarkan ID.

* **Endpoint**: `/books/{bookId}`
* **Method**: `PUT`
* **Body Request** (`application/json`):
    ```json
    {
        "name": "Buku A (Edisi Revisi)",
        "year": 2021,
        "author": "John Doe",
        "summary": "Ringkasan yang sudah diperbarui.",
        "publisher": "Dicoding Indonesia",
        "pageCount": 120,
        "readPage": 120,
        "reading": false
    }
    ```
* **Validasi**:
    * Aturan validasi sama seperti pada *endpoint* `POST /books` (nama wajib ada, `readPage` tidak boleh > `pageCount`).
* **Respon Sukses** (`200 OK`):
    ```json
    {
        "status": "success",
        "message": "Buku berhasil diperbarui"
    }
    ```
* **Respon Gagal** (`404 Not Found`): Terjadi jika `bookId` tidak ditemukan untuk diperbarui.
    ```json
    {
        "status": "fail",
        "message": "Gagal memperbarui buku. Id tidak ditemukan"
    }
    ```

---

### **5. Menghapus Buku**

Menghapus data buku berdasarkan ID.

* **Endpoint**: `/books/{bookId}`
* **Method**: `DELETE`
* **Respon Sukses** (`200 OK`):
    ```json
    {
        "status": "success",
        "message": "Buku berhasil dihapus"
    }
    ```
* **Respon Gagal** (`404 Not Found`): Terjadi jika `bookId` tidak ditemukan untuk dihapus.
    ```json
    {
        "status": "fail",
        "message": "Buku gagal dihapus. Id tidak ditemukan"
    }
    ```
