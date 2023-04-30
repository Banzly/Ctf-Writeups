No description chall :

Lets open web :
```js
            const check = (e) => {
                if (document.forms[0].username.value === "admin"){
                    if(swap(chunk(document.forms[0].password.value, 30)).join("") == "7e08250c4aaa9ed206fd7c9e398e2}actf{cl1ent_s1de_sucks_544e67ef12024523398ee02fe7517fffa92516317199e454f4d2bdb04d9e419ccc7"){
                        location.href="/win.html"
                    }
                    else{
                        document.getElementById("msg").style.display = "block"
                    }
                }
            }
        </script>
```
Kode tersebut merupakan bagian dari sebuah fungsi JavaScript bernama `check()`, yang akan dipanggil ketika tombol `submit` pada sebuah form di klik. 
Fungsi ini melakukan pengecekan terhadap input yang dimasukkan oleh pengguna pada form.
Jika nilai pada input dengan nama `username` sama dengan `admin`, 
maka fungsi akan melanjutkan pengecekan pada input dengan nama `password`.
Input pada password akan diubah menggunakan fungsi chunk() untuk memecah input menjadi beberapa bagian dengan panjang `30 karakter`, 
kemudian dibalik urutannya menggunakan fungsi `swap()`, dan digabungkan kembali menggunakan fungsi `join()`. 
Hasil dari penggabungan karakter akan dibandingkan dengan sebuah string panjang yang cukup panjang.
Jika nilai hasil penggabungan karakter sama dengan string panjang tersebut, maka pengguna akan diarahkan ke halaman `/win.html`.
Namun, jika nilai hasil penggabungan tidak sama, maka sebuah pesan dengan ID "msg" akan ditampilkan.

How To solve??
```
a = "7e08250c4aaa9ed206fd7c9e398e2}actf{cl1ent_s1de_sucks_544e67ef12024523398ee02fe7517fffa92516317199e454f4d2bdb04d9e419ccc7"
```
Next :
```
chunk(a, 30)
```
output : `['7e08250c4aaa9ed206fd7c9e398e2}', 'actf{cl1ent_s1de_sucks_544e67e', 'f12024523398ee02fe7517fffa9251', '6317199e454f4d2bdb04d9e419ccc7']`

`swap(chunk(a, 30))`

chunk dan swap adalah fungsi-fungsi yang tidak didefinisikan di dalam kode yang Anda berikan. Namun,
chunk digunakan untuk memecah string atau array menjadi potongan-potongan yang lebih kecil, 
sedangkan swap digunakan untuk menukar posisi elemen-elemen dalam suatu array atau string.
Dalam kasus kode yang Anda berikan, chunk(document.forms[0].password.value, 30) 
digunakan untuk memecah nilai yang diambil dari input dengan nama password menjadi potongan-potongan dengan panjang 30 karakter,
dan kemudian potongan-potongan ini diproses dengan fungsi swap.
```
['6317199e454f4d2bdb04d9e419ccc7', '7e08250c4aaa9ed206fd7c9e398e2}', 'f12024523398ee02fe7517fffa9251', 'actf{cl1ent_s1de_sucks_544e67e']
```
`swap(swap(chunk(a, 30))).join("")`

Fungsi ini `chunk()`digunakan untuk membagi string menjadi array substring dengan panjang tertentu. 
Dalam hal ini, dibutuhkan string adan membaginya menjadi array substring, yang masing-masing panjangnya `30 karakter`.
Fungsi `swap()`tidak didefinisikan dalam kode yang Anda berikan, tetapi dengan asumsi itu hanya membalikkan urutan elemen dalam array, 
`swap(chunk(a, 30))`akan mengembalikan array substring 30 karakter dalam urutan terbalik.
swap(swap(chunk(a, 30)))kemudian akan membalikkan urutan substring lagi, secara efektif mengembalikan array asli dari substring 30 karakter.
Akhirnya,` join("")`akan menggabungkan semua elemen array menjadi satu string tanpa pemisah, 
secara efektif menggabungkan substring 30 karakter kembali ke string asli a.
```
actf{cl1ent_s1de_sucks_544e67e6317199e454f4d2bdb04d9e419ccc7f12024523398ee02fe7517fffa92517e08250c4aaa9ed206fd7c9e398e2}
```

flag : ????
