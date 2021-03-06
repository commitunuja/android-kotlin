# Menyimpan data menggunakan SQLite

Menggunakan database adalah cara yang tepat untuk menyimpan data terstruktur atau data berulang, seperti informasi kontak. Halaman ini berasumsi bahwa Anda sudah familier dengan database SQL secara umum dan akan membantu Anda memulai database SQLite di Android. API yang nanti Anda perlukan untuk menggunakan database di Android tersedia dalam paket android.database.sqlite.

### Menentukan skema dan kontrak
Salah satu prinsip utama database SQL adalah skemanya: deklarasi formal tentang cara database diatur. Skema ini tercermin dalam Pernyataan SQL yang Anda gunakan untuk membuat database. Ada baiknya Anda membuat kelas pendamping yang disebut dengan kelas *kontrak*, yang secara eksplisit menetapkan tata letak skema Anda dalam cara yang sistematis dan terdokumentasi sendiri.

Kelas kontrak adalah penampung untuk konstanta yang menentukan nama URI, tabel, dan kolom. Kelas kontrak memungkinkan Anda menggunakan konstanta yang sama pada semua kelas lain dalam paket yang sama. Hal ini memungkinkan Anda mengubah nama kolom di satu tempat, kemudian mengatur agar perubahan tersebut disebarkan ke seluruh kode.

Cara yang tepat untuk mengatur kelas kontrak adalah dengan memberikan definisi yang bersifat global pada seluruh database Anda di tingkat root kelas tersebut. Kemudian, buat kelas dalam untuk setiap tabel. Setiap kelas dalam akan menghitung kolom tabel yang terkait.

Contohnya, kontrak berikut menentukan nama tabel dan nama kolom untuk satu tabel yang merepresentasikan feed RSS:

```kotlin
object FeedReaderContract {
    // Table contents are grouped together in an anonymous object.
    object FeedEntry : BaseColumns {
        const val TABLE_NAME = "entry"
        const val COLUMN_NAME_TITLE = "title"
        const val COLUMN_NAME_SUBTITLE = "subtitle"
    }
}
```
### Membuat database menggunakan SQL helper
Setelah menentukan tampilan database, Anda harus menerapkan metode yang akan membuat serta mengelola database dan tabel. Berikut adalah beberapa pernyataan umum untuk membuat dan menghapus tabel :

```kotlin
private const val SQL_CREATE_ENTRIES =
        "CREATE TABLE ${FeedEntry.TABLE_NAME} (" +
                "${BaseColumns._ID} INTEGER PRIMARY KEY," +
                "${FeedEntry.COLUMN_NAME_TITLE} TEXT," +
                "${FeedEntry.COLUMN_NAME_SUBTITLE} TEXT)"

private const val SQL_DELETE_ENTRIES = "DROP TABLE IF EXISTS ${FeedEntry.TABLE_NAME}"
```
Sama seperti file yang disimpan di penyimpanan internal perangkat, Android menyimpan database Anda dalam folder pribadi aplikasi. Data Anda akan selalu aman karena secara default area ini tidak dapat diakses oleh aplikasi lain atau oleh pengguna.

Kelas `SQLiteOpenHelper` berisi kumpulan API yang berguna untuk mengelola database Anda. Saat kelas ini digunakan untuk memperoleh referensi ke database, sistem hanya akan melakukan operasi pembuatan dan update database, yang mungkin memerlukan banyak waktu, hanya ketika diperlukan; bukan pada saat aplikasi dimulai. Yang perlu Anda lakukan hanyalah memanggil `getWritableDatabase()` atau `getReadableDatabase()`.

Untuk menggunakan `SQLiteOpenHelper`, buat subclass yang mengganti metode callback `onCreate()` dan `onUpgrade()`. Anda mungkin juga perlu menerapkan metode `onDowngrade()` atau `onOpen()`, tetapi keduanya tidak diperlukan.

Misalnya, berikut adalah penerapan `SQLiteOpenHelper` yang menggunakan beberapa perintah yang ditampilkan di atas :

```kotlin
class FeedReaderDbHelper(context: Context) : SQLiteOpenHelper(context, DATABASE_NAME, null, DATABASE_VERSION) {
    override fun onCreate(db: SQLiteDatabase) {
        db.execSQL(SQL_CREATE_ENTRIES)
    }
    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        // This database is only a cache for online data, so its upgrade policy is
        // to simply to discard the data and start over
        db.execSQL(SQL_DELETE_ENTRIES)
        onCreate(db)
    }
    override fun onDowngrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        onUpgrade(db, oldVersion, newVersion)
    }
    companion object {
        // If you change the database schema, you must increment the database version.
        const val DATABASE_VERSION = 1
        const val DATABASE_NAME = "FeedReader.db"
    }
}
```
Untuk mengakses database, buat instance subclass `SQLiteOpenHelper` Anda:

```kotlin
val dbHelper = FeedReaderDbHelper(context)
```

### Memasukkan informasi ke dalam database

Sisipkan data ke dalam database dengan meneruskan objek `ContentValues` ke metode `insert()`:

```kotlin
// Gets the data repository in write mode
val db = dbHelper.writableDatabase

// Create a new map of values, where column names are the keys
val values = ContentValues().apply {
    put(FeedEntry.COLUMN_NAME_TITLE, title)
    put(FeedEntry.COLUMN_NAME_SUBTITLE, subtitle)
}

// Insert the new row, returning the primary key value of the new row
val newRowId = db?.insert(FeedEntry.TABLE_NAME, null, values)
```

Argumen pertama untuk `insert()` cukup berisi nama tabel.

Argumen kedua memberi tahu framework tentang apa yang harus dilakukan jika `ContentValues` kosong (yaitu, Anda tidak `put` (memasukkan) nilai apa pun). Jika Anda menetapkan nama sebuah kolom, framework akan menyisipkan baris dan menetapkan nilai kolom tersebut ke null. Jika Anda menetapkan `null`, seperti dalam contoh kode ini, framework tidak akan menyisipkan baris ketika nilai tidak ada.

Metode `insert()` mengembalikan ID untuk baris yang baru dibuat, atau akan menampilkan -1 jika terjadi error saat memasukkan data. Hal ini dapat terjadi jika ada konflik dengan data yang sudah ada dalam database.

### Membaca informasi dari database

Untuk membaca dari database, gunakan metode `query()`, dengan meneruskan kriteria pemilihan dan kolom yang diinginkan. Metode ini menggabungkan elemen `insert()` dan `update()`, tetapi daftar kolomnya menentukan data yang ingin diambil ("proyeksi"), bukan data yang akan dimasukkan. Hasil kueri ditampilkan kepada Anda dalam objek `Cursor`.

```kotlin
val db = dbHelper.readableDatabase

// Define a projection that specifies which columns from the database
// you will actually use after this query.
val projection = arrayOf(BaseColumns._ID, FeedEntry.COLUMN_NAME_TITLE, FeedEntry.COLUMN_NAME_SUBTITLE)

// Filter results WHERE "title" = 'My Title'
val selection = "${FeedEntry.COLUMN_NAME_TITLE} = ?"
val selectionArgs = arrayOf("My Title")

// How you want the results sorted in the resulting Cursor
val sortOrder = "${FeedEntry.COLUMN_NAME_SUBTITLE} DESC"

val cursor = db.query(
        FeedEntry.TABLE_NAME,   // The table to query
        projection,             // The array of columns to return (pass null to get all)
        selection,              // The columns for the WHERE clause
        selectionArgs,          // The values for the WHERE clause
        null,                   // don't group the rows
        null,                   // don't filter by row groups
        sortOrder               // The sort order
)
```

Argumen ketiga dan keempat (`selection` and `selectionArgs`) digabungkan untuk membuat klausa WHERE. Argumen ini diberikan secara terpisah dari kueri pemilihan sehingga akan dikecualikan sebelum digabungkan. Oleh karena itu, pernyataan pemilihan Anda tidak akan terpengaruh oleh penambahan SQL. Untuk mengetahui detail selengkapnya tentang semua argumen, lihat referensi `query()`.

ntuk melihat baris dalam kursor, gunakan salah satu metode pemindahan `Cursor`, yang harus selalu Anda panggil sebelum mulai membaca nilai. Kursor dimulai pada posisi -1 sehingga memanggil `moveToNext()` akan menempatkan "posisi baca" pada entri pertama dalam hasil dan mengembalikan apakah kursor sudah melewati entri terakhir dalam kumpulan hasil atau belum. Untuk setiap baris, Anda dapat membaca nilai kolom dengan memanggil salah satu metode get `Cursor`, seperti `getString()` atau `getLong()`. Untuk setiap metode get, Anda harus meneruskan posisi indeks kolom yang Anda inginkan, yang bisa Anda dapatkan dengan memanggil `getColumnIndex()` atau `getColumnIndexOrThrow()`. Setelah selesai mengiterasi hasilnya, panggil `close()` pada kursor untuk melepaskan resource-nya. Misalnya, berikut adalah cara mendapatkan semua ID item yang disimpan dalam kursor dan menambahkannya ke daftar :

```kotlin
val itemIds = mutableListOf<Long>()
with(cursor) {
    while (moveToNext()) {
        val itemId = getLong(getColumnIndexOrThrow(BaseColumns._ID))
        itemIds.add(itemId)
    }
}
```

### Menghapus informasi dari database

Untuk menghapus baris dari tabel, Anda harus memberikan kriteria pemilihan yang mengidentifikasi baris ke metode `delete()`. Mekanismenya bekerja dalam cara yang sama seperti argumen pemilihan untuk metode `query()`. Proses ini membagi spesifikasi pemilihan menjadi klausa pemilihan dan argumen pemilihan. Klausa menentukan kolom yang harus dilihat, juga memungkinkan Anda untuk menggabungkan proses pengujian kolom. Argumen adalah nilai yang akan digunakan untuk pengujian, yang terikat dengan klausa tersebut. Karena hasilnya tidak ditangani seperti Pernyataan SQL biasa, penambahan SQL pun tidak akan berpengaruh.

```kotlin
// Define 'where' part of query.
val selection = "${FeedEntry.COLUMN_NAME_TITLE} LIKE ?"
// Specify arguments in placeholder order.
val selectionArgs = arrayOf("MyTitle")
// Issue SQL statement.
val deletedRows = db.delete(FeedEntry.TABLE_NAME, selection, selectionArgs)
```

### Mengupdate database
Bila Anda perlu memodifikasi subset nilai database, gunakan metode `update()`.
Memperbarui tabel akan menggabungkan sintaks `ContentValues` dari `insert()` dengan sintaks `WHERE` dari `delete()`.  

```kotlin
val db = dbHelper.writableDatabase

// New value for one column
val title = "MyNewTitle"
val values = ContentValues().apply {
    put(FeedEntry.COLUMN_NAME_TITLE, title)
}

// Which row to update, based on the title
val selection = "${FeedEntry.COLUMN_NAME_TITLE} LIKE ?"
val selectionArgs = arrayOf("MyOldTitle")
val count = db.update(
        FeedEntry.TABLE_NAME,
        values,
        selection,
        selectionArgs)       
```

Nilai hasil dari metode `update()` adalah jumlah baris yang terpengaruh dalam database.

### Mempertahankan koneksi database

Karena `getWritableDatabase()` dan `getReadableDatabase()` sulit dipanggil jika database ditutup, koneksi database harus tetap terbuka selama Anda perlu mengaksesnya. Biasanya, akan lebih optimal untuk menutup database dalam `onDestroy()` Aktivitas pemanggilan.


```kotlin
override fun onDestroy() {
    dbHelper.close()
    super.onDestroy()
}
```