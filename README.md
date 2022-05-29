# Web_Genshin_Builder
Repo ini akan membuat web lokal Genshin Builder, yaitu sebuah web yang dapat menyimpan dan menampilkan Build untuk karakter dari game Genshin Impact
Build pada web ini akan memuat karakter, senjata, serta 5 artifak saja.

## How to use
1. Landing page, bisa melihat seluruh build yang ada pada page ini, dan melakukan login/register
2. silahkan coba register, pastikan username tidak sama seperti akun yang sudah ada sebelumnya
3. jika sudah bisa, silahkan login untuk melihat menu personal build
4. jika sudah login, build pada landing page bisa di copy ke personal build
5. personal build page akan memuat seluruh build yang dimiliki akun tersebut
6. di page ini kita juga dapat menghapus build yang ada dan membuat build baru, serta melakukan pembaharuan pada build

## How to install
1. Download dan jalankan pg_restore untuk folder ```dumpGenshinBuilder.sql``` ke database PSQL yang disiapkan
2. Sesuaikan objek ```Client``` pada ```index.js``` ke database PSQL yang disiapkan sebelumnya
3. Run kode javascript tersebut
