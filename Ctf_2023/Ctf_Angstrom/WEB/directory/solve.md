This is one of the directories of all time, and I would definitely rate it out of 10.

Author: JoshDaBosh

Lets Open web : 
Simple web yang terdapat 5000 folder didalam web tersebut.

ketika saya mencoba melihat satu folder hanya muncul text : `your flag is in another file`

# How To Solve?

```js
wget -r -np https://directory.web.actf.co/

# -r ke halaman lain di situs web yang sama dan mengunduhnya juga.
# -np Opsi dari url yang akan dimulai

# grep -r "actf" .
```

flag : `actf{y0u_f0und_me_b51d0cde76739fa3}`
