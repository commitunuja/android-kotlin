#LinearLayout

LinearLayout adalah sekelompok tampilan yang menyejajarkan semua anak dalam satu arah, secara vertikal atau horizontal. Anda bisa menetapkan arah layout dengan atributandroid:orientation.
Semua anak LinearLayout akan ditumpuk satu sama lain, jadi daftar vertikal hanya akan memiliki satu anak per baris, seberapa pun lebarnya, dan daftar horizontal hanya akan setinggi satu baris (tinggi anak yang tertinggi, ditambah pengisi). LinearLayout akan mengikuti margin antara anak dan gravity (sejajar kanan, tengah, atau kiri) setiap anak (child).
![](https://miro.medium.com/max/325/1*maf0Or2c119yNn-cxHS0eA.png)

#RelativeLayout

RelativeLayout adalah Layout ini menampilkan komponen dalam posisi relatif. Posisi masing-masing Layout dapat ditentukan sebagai relatif terhadap elemen saudara (seperti di sebelah kiri atau di bawah tampilan lain) atau pada posisi relatif terhadap area RelativeLayout induk (seperti sejajar dengan bagian bawah, kiri atau tengah).
Jadi, dengan menggunakan RelativeLayout, kita dapat menyusun suatu komponen secara relatif terhadap komponen lainnya.
!.[].(https://miro.medium.com/max/117/1*4T3J1jr2PvIMYbEW-1Gdpg.png)

#FrameLayout

Layout ini adalah layout yang paling sederhana. Layout ini akan membuat komponen yang ada didalamnya menjadi menumpuk atau saling menutupi satu dengan yang lainnya (layering). Komponen yang paling pertama pada layout ini akan berada dibawah komponen-komponen diatasnya. Pada penggunaan fragment, FrameLayout memiliki kemampuan untuk menjadi container untuk tiap fragment didalam sebuah Activity. Berikut ilustrasi dari penggunaan FrameLayout terhadap child view yang dimiliki didalamnya.
!.[].(https://miro.medium.com/max/398/1*E2gII-415FrMEXf0VVk4Nw.png)

#GridLayout

GridLayout ini diperkenalkan pada API level 14 (Android 4.0 / Ice Cream Sandwich), layout ini akan memberikan kemudahan dengan mengakomodir komponen didalamnya ke dalam bentuk Grid (Kolom dan Baris). Dalam sebuah referensi, GridLayout merupakan komponen Layout yang sangat fleksibel dan dapat dimanfaatkan untuk menyederhanakan pembuatan Layout UI yang bersifat kompleks dan bersarang yang terdapat di komponen Layout lainnya.
!.[].(https://miro.medium.com/max/400/1*DfzmlzH8HBcEDxmEEAdleA.png)


#ScrollView

ScrollView adalah sebuah Layout yang akan membuat komponen didalam dapat digeser (scroll) secara vertikal dan horizontal. Dengan ScrollView, dimungkinkan ukuran komponen didalamnya melebihi ukuran screen. Komponen didalam scrollview hanya diperbolehkan memiliki 1 parent utama dari layout linear, relatif, frame, atau grid layout.
Berikut contoh dari Layout ScrollView :
!.[].(https://miro.medium.com/max/622/1*_xjPFx7dZ87Wx-O5si2bUg.png)