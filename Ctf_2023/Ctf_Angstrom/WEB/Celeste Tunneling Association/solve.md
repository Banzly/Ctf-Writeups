Welcome to the tunnels!! Have fun!

Here's the source

Author: paper

Let' Open web :
`Welcome to the _tunnel_. Watch your step!!`

view backsource : 
```js
# run via `uvicorn app:app --port 6000`
import os

SECRET_SITE = b"flag.local"
FLAG = os.environ['FLAG']

async def app(scope, receive, send):
    assert scope['type'] == 'http'

    headers = scope['headers']

    await send({
        'type': 'http.response.start',
        'status': 200,
        'headers': [
            [b'content-type', b'text/plain'],
        ],
    })

    # IDK malformed requests or something
    num_hosts = 0
    for name, value in headers:
        if name == b"host":
            num_hosts += 1

    if num_hosts == 1:
        for name, value in headers:
            if name == b"host" and value == SECRET_SITE:
                await send({
                    'type': 'http.response.body',
                    'body': FLAG.encode(),
                })
                return

    await send({
        'type': 'http.response.body',
        'body': b'Welcome to the _tunnel_. Watch your step!!',
    })
```

# Recon
Disini kode program Python yang menggunakan framework ASGI (Asynchronous Server Gateway Interface) untuk membuat sebuah web server yang memberikan respon berupa teks sederhana. 
Berikut penjelasan detail mengenai setiap bagian dari script tersebut:
Mengimport modul os untuk mengambil environment variable, yaitu variabel FLAG.

Deklarasi variabel SECRET_SITE
```js 
SECRET_SITE = b"flag.local"
```
Menetapkan variabel `SECRET_SITE` dengan nilai `b"flag.local"` yang akan digunakan untuk membandingkan dengan header host dalam request.

Deklarasi variabel FLAG
```js
FLAG = os.environ['FLAG']
```
Mengambil nilai environment variable FLAG menggunakan modul os.

Deklarasi fungsi app
```js
async def app(scope, receive, send):
    assert scope['type'] == 'http'
```
Mendefinisikan fungsi app yang merupakan bagian inti dari web server. 
Fungsi ini menerima tiga parameter yaitu` scope, receive, dan send`. Variabel scope berisi informasi mengenai tipe protokol (dalam hal ini HTTP),
sedangkan receive dan send adalah coroutine yang digunakan untuk menerima dan mengirim data ke client.

Deklarasi variabel headers :
```js
headers = scope['headers']
```
Menyimpan header request yang diterima dari client dalam variabel headers.

Mengirim response dengan status code 200 dan tipe konten text/plain
```js
await send({
    'type': 'http.response.start',
    'status': 200,
    'headers': [
        [b'content-type', b'text/plain'],
    ],
})
```
Mengirim respon ke client dengan menggunakan coroutine send. Status code yang dikirim adalah 200 dan tipe konten yang dikirim adalah text/plain.

Memeriksa jumlah header host yang ada dalam request
```js
num_hosts = 0
for name, value in headers:
    if name == b"host":
        num_hosts += 1
```
Memeriksa apakah terdapat lebih dari satu header host dalam request dengan menghitung jumlahnya. Jika ada lebih dari satu,
maka request dianggap bermasalah dan tidak diproses lebih lanjut.

Memeriksa apakah header host dalam request sama dengan `SECRET_SITE`
```js
if num_hosts == 1:
    for name, value in headers:
        if name == b"host" and value == SECRET_SITE:
            await send({
                'type': 'http.response.body',
                'body': FLAG.encode(),
            })
            return
```
Jika terdapat satu header host dalam request, 
maka memeriksa apakah valuenya sama dengan SECRET_SITE. Jika sama,
maka mengirimkan respon dengan body berupa flag yang disimpan dalam variabel FLAG.

Mengirim respon default jika tidak ada kondisi yang terpenuhi sebelumnya
```js
await send({
    'type': 'http.response.body',
    'body': b'Welcome to the _tunnel_. Watch your step!!',
})
```

# How To Solve??

```js
import requests

r = requests.get("url", headers={"host": "flag.local"})
print(r.text)
```

Flag : `actf{reaching_the_core__chapter_8}`



