# Desain Material untuk Android

Desain material adalah panduan komprehensif untuk desain visual, gerakan, dan interaksi di seluruh platform dan perangkat. Untuk menggunakan desain material di aplikasi Android, ikuti panduan yang ditetapkan dalam [spesifikasi desain material](https://material.io/design) dan gunakan komponen dan gaya baru yang tersedia di [support library desain material](https://developer.android.com/topic/libraries/support-library/features?hl=id#material-design). Halaman ini menyediakan ringkasan tentang pola dan API yang harus Anda gunakan.

Android menyediakan fitur berikut untuk membantu Anda mem-build aplikasi yang didasarkan pada desain material:

- Tema aplikasi desain material untuk menetapkan gaya widget UI Anda.
- Widget untuk tampilan kompleks seperti daftar dan kartu
- API baru untuk bayangan dan animasi kustom

### Tema dan widget material

Untuk memanfaatkan fitur material seperti penataan gaya untuk widget UI standar, dan untuk menyederhanakan definisi gaya aplikasi, terapkan tema berbasis material ke aplikasi Anda.

![Foto](https://raw.githubusercontent.com/idlukman/android-kotlin/main/foto/android.PNG)

Untuk informasi selengkapnya, lihat cara [menerapkan tema material](https://developer.android.com/guide/topics/ui/look-and-feel/themes?hl=id#MaterialTheme).

<p>Untuk memberikan pengalaman yang tidak asing bagi pengguna, gunakan pola UX material yang paling umum :</p>

- Promosikan tindakan utama UI Anda dengan [Tombol Tindakan Mengambang](https://developer.android.com/guide/topics/ui/floating-action-button?hl=id) (FAB).
- Tampilkan brand, navigasi, penelusuran, dan tindakan lainnya dengan [Panel Aplikasi](https://developer.android.com/training/appbar?hl=id)
- Tampilkan dan sembunyikan navigasi aplikasi Anda dengan [Panel Navigasi](https://developer.android.com/guide/navigation/navigation-ui?hl=id#add_a_navigation_drawer).
- Gunakan salah satu dari banyak komponen material lainnya untuk tata letak dan navigasi aplikasi Anda, seperti toolbar yang dapat diciutkan, tab, menu navigasi bawah, dan lainnya. Untuk melihat semua komponen material, lihat [katalog Komponen Material untuk Android](https://material.io/develop/android/components).

Jika memungkinkan, gunakan ikon material standar. Misalnya, tombol "menu" navigasi untuk panel navigasi harus menggunakan ikon tiga garis (hamburger) standar. Lihat [Ikon Desain Material](https://material.io/resources/icons/) untuk daftar ikon yang tersedia. Anda juga dapat mengimpor ikon SVG dari library ikon material dengan [Vector Asset Studio](https://developer.android.com/studio/write/vector-asset-studio?hl=id#importing) Android Studio.

### Ketinggian bayangan dan kartu

<p>Selain properti X dan Y, tampilan di Android memiliki properti Z. Properti baru ini menunjukkan ketinggian tampilan, yang menentukan: </p>

- Ukuran bayangan: tampilan dengan nilai Z yang lebih tinggi menghasilkan bayangan yang lebih besar.
- Urutan gambar: tampilan dengan nilai Z yang lebih tinggi akan muncul di bagian atas tampilan lain.

![Foto](https://raw.githubusercontent.com/idlukman/android-kotlin/main/foto/conten.PNG)

Ketinggian sering kali diterapkan saat tata letak Anda menyertakan tata letak berbasis kartu, yang membantu Anda menampilkan bagian informasi penting di dalam kartu yang menggunakan tampilan material. Anda dapat menggunakan widget [CardView](https://developer.android.com/reference/androidx/cardview/widget/CardView?hl=id) untuk membuat kartu dengan ketinggian default. Untuk informasi selengkapnya, lihat [Membuat Tata Letak Berbasis Kartu](https://developer.android.com/guide/topics/ui/layout/cardview?hl=id).

Untuk informasi tentang cara menambahkan ketinggian ke tampilan lain, lihat [Membuat Bayangan dan Tampilan Klip](https://developer.android.com/training/material/shadows-clipping?hl=id).

### Animasi

API animasi baru memungkinkan Anda membuat animasi kustom untuk masukan sentuhan di kontrol UI, perubahan status tampilan, dan transisi aktivitas.

<p>API ini memungkinkan Anda :</p>

- Menanggapi peristiwa sentuhan di tampilan Anda dengan animasi **masukan sentuhan**.
- Menyembunyikan dan menampilkan tampilan dengan animasi **reveal melingkar**.
- Beralih antar-aktivitas dengan animasi **transisi aktivitas** kustom.
- Membuat animasi yang lebih natural dengan **gerakan melengkung**.
- Menganimasikan perubahan dalam satu atau beberapa properti tampilan dengan animasi **perubahan status tampilan**.
- Menampilkan animasi dalam **daftar status drawable** di antara perubahan status tampilan.

Animasi masukan sentuh disertakan dalam beberapa tampilan standar, seperti tombol. API baru memungkinkan Anda menyesuaikan animasi tersebut dan menambahkannya ke tampilan kustom Anda.

Untuk informasi selengkapnya, lihat [Ringkasan Animasi](https://developer.android.com/training/animation/overview?hl=id).

### Drawable

<p>Kemampuan baru untuk drawable ini membantu Anda mengimplementasikan aplikasi desain material :</p>

- **Vektor drawable** dapat diskalakan tanpa kehilangan definisi dan sangat cocok untuk ikon dalam aplikasi dengan satu warna. Pelajari [vektor drawable](https://developer.android.com/guide/topics/graphics/vector-drawable-resources?hl=id) lebih lanjut.

- **Menambahkan tint drawable** memungkinkan Anda menentukan bitmap sebagai mask alfa dan menambahkan tint dengan warna pada saat runtime. Lihat cara [menambahkan tint ke drawable](https://developer.android.com/guide/topics/graphics/drawables?hl=id#DrawableTint).

- **Ekstraksi warna** memungkinkan Anda otomatis mengekstrak warna yang menonjol dari gambar bitmap. Lihat cara [memilih warna dengan Palette API](https://developer.android.com/training/material/palette-colors?hl=id).
