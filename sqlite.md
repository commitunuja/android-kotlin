# Menyimpan data menggunakan SQLite

Menggunakan database adalah cara yang tepat untuk menyimpan data terstruktur atau data berulang, seperti informasi kontak. Halaman ini berasumsi bahwa Anda sudah familier dengan database SQL secara umum dan akan membantu Anda memulai database SQLite di Android. API yang nanti Anda perlukan untuk menggunakan database di Android tersedia dalam paket android.database.sqlite.

#### Menentukan skema dan kontrak
Salah satu prinsip utama database SQL adalah skemanya: deklarasi formal tentang cara database diatur. Skema ini tercermin dalam Pernyataan SQL yang Anda gunakan untuk membuat database. Ada baiknya Anda membuat kelas pendamping yang disebut dengan kelas *kontrak*, yang secara eksplisit menetapkan tata letak skema Anda dalam cara yang sistematis dan terdokumentasi sendiri.