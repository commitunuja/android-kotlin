# Menyimpan data menggunakan SQLite

Menggunakan database adalah cara yang tepat untuk menyimpan data terstruktur atau data berulang, seperti informasi kontak. Halaman ini berasumsi bahwa Anda sudah familier dengan database SQL secara umum dan akan membantu Anda memulai database SQLite di Android. API yang nanti Anda perlukan untuk menggunakan database di Android tersedia dalam paket android.database.sqlite.

#### Menentukan skema dan kontrak
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
#### Membuat database menggunakan SQL helper
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
