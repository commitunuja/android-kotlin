## Event 

Di Android, ada berbagai cara untuk mencegat peristiwa dari interaksi pengguna dengan aplikasi. Saat mempertimbangkan peristiwa dalam antarmuka pengguna, pendekatannya adalah menangkap peristiwa dari objek View tertentu yang digunakan pengguna untuk berinteraksi. Class View menyediakan sarana untuk melakukannya.

Dalam berbagai class View yang akan digunakan untuk menyusun tata letak, Anda mungkin melihat beberapa metode callback publik yang tampak berguna untuk peristiwa UI. Metode ini dipanggil oleh framework Android saat masing-masing tindakan terjadi pada objek tersebut. Misalnya, jika View (seperti Button) disentuh, metode `onTouchEvent()` akan dipanggil pada objek tersebut. Namun, untuk mencegatnya, Anda harus memperluas class dan mengganti metodenya. Akan tetapi, memperluas setiap objek View untuk menangani peristiwa tersebut tidak praktis. Karena itu, class View juga berisi sekumpulan antarmuka bertingkat dengan callback yang jauh lebih mudah ditentukan. Antarmuka ini, yang disebut  pemroses peristiwa, merupakan tiket Anda untuk menangkap interaksi pengguna dengan UI.

Walaupun Anda akan lebih sering menggunakan pemroses peristiwa untuk interaksi pengguna, mungkin ada saatnya Anda ingin memperluas class View untuk mem-build komponen kustom. Mungkin Anda ingin memperluas class `Button` untuk membuat sesuatu yang lebih menarik. Dalam hal ini, perilaku peristiwa default untuk class Anda akan dapat ditentukan menggunakan class pengendali peristiwa.

#### Pemroses peristiwa

Pemroses peristiwa merupakan antarmuka di class `View` yang berisi satu metode callback. Metode ini akan dipanggil oleh framework Android jika View tempat pemroses didaftarkan dipicu oleh interaksi pengguna dengan item pada UI.

Metode callback berikut juga disertakan dalam antarmuka pemroses peristiwa:

`onClick()`

Dari `View.OnClickListener`. Metode ini dipanggil bila pengguna menyentuh item (saat dalam mode sentuh). atau berfokus pada item dengan tombol navigasi atau trackball, lalu menekan tombol "enter" yang sesuai atau menekan trackball.

`onLongClick()`

Dari `View.OnLongClickListener`. Metode ini dipanggil bila pengguna menyentuh lama item (saat dalam mode sentuh), atau berfokus pada item dengan tombol navigasi atau trackball, lalu menekan dan menahan tombol "enter" yang sesuai atau menekan dan menahan trackball (selama satu detik).

`onFocusChange()`

Dari `View.OnFocusChangeListener`. Metode ini dipanggil bila pengguna menavigasi ke atau menjauh dari item, menggunakan tombol navigasi atau trackball.

`onKey()`

Dari `View.OnKeyListener`. Metode ini dipanggil bila pengguna berfokus pada item dan menekan atau melepas tombol hardware pada perangkat.

`onTouch()`

Dari `View.OnTouchListener`. Metode ini dipanggil bila pengguna melakukan tindakan yang digolongkan sebagai peristiwa sentuh, termasuk penekanan, pelepasan, atau gestur perpindahan pada layar (dalam batasan item).

`onCreateContextMenu()`

Dari `View.OnCreateContextMenuListener`. Metode ini dipanggil bila Menu Konteks sedang di-build (sebagai hasil dari "klik panjang" berkelanjutan).

Metode ini adalah satu-satunya yang menempati antarmukanya masing-masing. Untuk menentukan salah satu metode ini dan menangani peristiwa, implementasikan antarmuka yang disarangkan dalam Activity atau tentukan sebagai class anonim. Kemudian, teruskan satu instance implementasi ke masing-masing metode `View.set...Listener()`. (Misalnya, panggil `setOnClickListener()` dan teruskan implementasi `OnClickListener`.)

Contoh di bawah menunjukkan cara mendaftarkan pemroses untuk Button.

```kotlin
protected void onCreate(savedValues: Bundle) {
    ...
    val button: Button = findViewById(R.id.corky)
    // Register the onClick listener with the implementation above
    button.setOnClickListener { view ->
        // do something when the button is clicked
    }
    ...
}
```

Mengimplementasikan OnClickListener sebagai bagian dari Activity juga mungkin terasa lebih mudah bagi Anda. Hal ini akan menghindari beban class tambahan dan alokasi objek. Contoh:

```kotlin
class ExampleActivity : Activity(), OnClickListener {

    protected fun onCreate(savedValues: Bundle) {
        val button: Button = findViewById(R.id.corky)
        button.setOnClickListener(this)
    }

    // Implement the OnClickListener callback
    fun onClick(v: View) {
        // do something when the button is clicked
    }
}
```

Perlu diketahui bahwa callback `onClick()` pada contoh di atas tidak menampilkan nilai, tetapi beberapa metode pemroses peristiwa lainnya harus menampilkan boolean. Alasannya bergantung pada peristiwa. Untuk beberapa yang menampilkan boolean, berikut alasannya:

- `onLongClick()` - Ini menampilkan boolean untuk menunjukkan apakah peristiwa telah dipakai dan tidak boleh dilakukan lebih jauh. Artinya, menampilkan true untuk menunjukkan bahwa peristiwa telah ditangani dan harus berhenti di sini; menampilkan false jika Anda tidak menanganinya dan/atau peristiwa harus melanjutkan ke pemroses berbasis klik lainnya.
- `onKey()` - Ini menampilkan boolean untuk menunjukkan apakah peristiwa telah dipakai dan tidak boleh dilakukan lebih jauh. Artinya, menampilkan true untuk menunjukkan bahwa peristiwa telah ditangani dan harus berhenti di sini; menampilkan false jika Anda tidak menanganinya dan/atau peristiwa harus melanjutkan ke pemroses berbasis tombol lainnya.
- `onTouch()` - Ini menampilkan boolean untuk menunjukkan apakah pemroses telah memakai peristiwa ini. Satu hal yang penting adalah peristiwa ini dapat memiliki beberapa tindakan yang saling mengikuti. Jadi, jika nilai false ditampilkan saat peristiwa tindakan ke bawah diterima, berarti Anda belum memakai peristiwa tersebut dan juga tidak tertarik dengan tindakan berikutnya dari peristiwa ini. Karena itu, Anda tidak akan dipanggil untuk tindakan lainnya dalam peristiwa, seperti gestur jari, atau peristiwa tindakan ke atas yang akan terjadi.

Ingat bahwa peristiwa tombol hardware selalu dikirimkan ke View yang saat ini menjadi fokus. Peristiwa ini dikirim mulai dari atas hierarki View, kemudian turun hingga mencapai tujuan yang sesuai. Jika View (atau turunan View) saat ini memiliki fokus, maka Anda dapat melihat peristiwa berpindah melalui metode `dispatchKeyEvent()`. Sebagai alternatif untuk menangkap peristiwa tombol melalui View, Anda juga dapat menerima semua peristiwa dalam Activity dengan `onKeyDown()` dan `onKeyUp()`.

Selain itu, saat memikirkan tentang input teks aplikasi, ingat bahwa banyak perangkat yang hanya memiliki metode input software. Metode tersebut tidak harus berbasis tombol; sebagian mungkin menggunakan input suara, tulisan tangan, dan sebagainya. Meskipun menampilkan antarmuka seperti keyboard, metode input umumnya **tidak** memicu lini `onKeyDown()` peristiwa. Jangan pernah mem-build UI yang mengharuskan penekanan tombol tertentu dikontrol, kecuali jika Anda ingin membatasi aplikasi hanya untuk perangkat yang memiliki keyboard hardware. Secara khusus, jangan andalkan metode ini untuk memvalidasi input saat pengguna menekan tombol kembali; sebagai gantinya, gunakan tindakan seperti `IME_ACTION_DONE` untuk memberi tahu metode input tentang reaksi yang diharapkan aplikasi, sehingga dapat mengubah UI-nya secara signifikan. Hindari asumsi tentang cara metode input software seharusnya bekerja dan percayalah bahwa metode input akan menyediakan teks berformat untuk aplikasi.

#### Pengendali peristiwa

Jika mem-build komponen kustom dari View, maka Anda akan dapat menentukan beberapa metode callback yang digunakan sebagai pengendali peristiwa default. Dalam dokumen tentang Komponen Tampilan Kustom, Anda akan mempelajari beberapa callback umum yang digunakan untuk penanganan peristiwa, termasuk:

- `onKeyDown(int, KeyEvent)` - Dipanggil saat peristiwa tombol baru terjadi.
- `onKeyUp(int, KeyEvent)` - Dipanggil saat peristiwa tombol ke atas terjadi.
- `onTrackballEvent(MotionEvent)` - Dipanggil saat peristiwa gerakan trackball terjadi.
- `onTouchEvent(MotionEvent)` - Dipanggil saat peristiwa gerakan layar sentuh terjadi.
- `onFocusChanged(boolean, int, Rect)` - Dipanggil saat tampilan mendapatkan atau kehilangan fokus.

Ada beberapa metode lainnya yang harus Anda ketahui, yang bukan bagian dari class View, tetapi bisa berdampak langsung pada cara Anda menangani peristiwa. Jadi, saat mengelola peristiwa yang lebih kompleks dalam tata letak, pertimbangkan metode-metode lain ini:

- `Activity.dispatchTouchEvent(MotionEvent)` - Metode ini memungkinkan `Activity` mencegat semua peristiwa sentuh sebelum dikirim ke jendela.
- `ViewGroup.onInterceptTouchEvent(MotionEvent)` - Metode ini memungkinkan `ViewGroup` memantau peristiwa saat dikirim ke View turunan.
- `ViewParent.requestDisallowInterceptTouchEvent(boolean)` - Panggil metode ini pada View induk untuk menunjukkan larangan mencegat peristiwa sentuh dengan `onInterceptTouchEvent(MotionEvent)`.

#### Mode sentuh

Saat pengguna menavigasi antarmuka pengguna dengan tombol arah atau trackball, Anda harus memberi fokus pada item yang dapat ditindaklanjuti (seperti tombol) agar pengguna dapat mengetahui apa yang akan menerima input. Namun, jika perangkat memiliki kemampuan sentuh, dan pengguna mulai berinteraksi dengan menyentuh antarmuka, maka Anda tidak perlu lagi menyorot item, atau memberi fokus pada View tertentu. Jadi, ada mode untuk interaksi yang bernama "mode sentuh".

Untuk perangkat berkemampuan sentuh, setelah pengguna menyentuh layar, perangkat akan masuk ke mode sentuh. Dari sini dan selanjutnya, hanya View untuk `isFocusableInTouchMode()` bernilai true yang akan dapat difokuskan, seperti widget pengedit teks. View lain yang dapat disentuh, seperti tombol, tidak akan mengambil fokus jika disentuh; View tersebut akan langsung memicu pemroses berbasis klik ditekan.

Kapan pun pengguna menekan tombol arah atau men-scroll dengan trackball, perangkat akan keluar dari mode sentuh, dan mencari tampilan untuk difokuskan. Kini pengguna dapat melanjutkan interaksi dengan antarmuka pengguna tanpa menyentuh layar.

Status mode sentuh dipertahankan di seluruh sistem (semua jendela dan aktivitas). Untuk mengajukan kueri terkait status saat ini, Anda dapat memanggil `isInTouchMode()` untuk mengetahui apakah perangkat sedang dalam mode sentuh.

#### Menangani fokus

Framework ini akan menangani gerakan fokus rutin sebagai respons input pengguna. Ini termasuk mengubah fokus saat View dihapus atau disembunyikan, atau saat View baru tersedia. View menunjukkan kesediaannya untuk mengambil fokus melalui metode `isFocusable()`. Untuk mengubah apakah View dapat mengambil fokus, panggil `setFocusable()`. Saat dalam mode sentuh, Anda dapat mengajukan kueri apakah View memungkinkan fokus dengan `isFocusableInTouchMode()`. Anda dapat mengubahnya dengan `setFocusableInTouchMode()`.

Gerakan fokus didasarkan pada algoritme yang mencari tetangga terdekat ke arah tertentu. Dalam kasus yang jarang terjadi, algoritme default mungkin tidak cocok dengan perilaku yang dimaksudkan developer. Dalam situasi ini, Anda dapat menyediakan penggantian eksplisit dengan atribut XML berikut dalam file tata letak: ***nextFocusDown***, ***nextFocusLeft***, ***nextFocusRight***, dan ***nextFocusUp***. Tambahkan salah satu atribut ini ke View dari tempat fokus menghilang. Tentukan nilai atribut untuk menjadi ID View ke tempat fokus harus diberikan. Contoh:

```xml
<LinearLayout
    android:orientation="vertical"
    ... >
  <Button android:id="@+id/top"
          android:nextFocusUp="@+id/bottom"
          ... />
  <Button android:id="@+id/bottom"
          android:nextFocusDown="@+id/top"
          ... />
</LinearLayout>
```

Dalam tata letak vertikal ini, biasanya menavigasi ke atas dari Button pertama tidak akan membawa ke mana pun, juga tidak akan menavigasi ke bawah dari Button kedua. Setelah Button atas menentukan Button bawah sebagai ***nextFocusUp*** (dan sebaliknya), fokus navigasi akan beralih dari atas ke bawah dan dari bawah ke atas.

Jika Anda ingin mendeklarasikan View sebagai elemen yang dapat difokuskan dalam UI (jika biasanya bukan), tambahkan atribut XML `android:focusable` ke View dalam deklarasi tata letak. Tetapkan nilai ***true***. Anda juga dapat mendeklarasikan View sebagai elemen yang dapat difokuskan saat dalam Mode Sentuh dengan `android:focusableInTouchMode`.

Untuk meminta View tertentu mengambil fokus, panggil `requestFocus()`.

Untuk memproses peristiwa fokus (diberi tahu bila View menerima atau kehilangan fokus), gunakan `onFocusChange()`.