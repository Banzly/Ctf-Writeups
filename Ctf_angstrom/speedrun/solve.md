Celeste Speedrunning Associationweb20704
I love Celeste Speedrunning so much!!! It's so funny to watch!!!

Here's my favorite site!

Author: paper

# Recon
Pada saat masuk webiste terdapat definisi chall :

Welcome to Celeste speedrun records!!!
Current record holders (beat them at /play for a flag!):
Old Lady: 0 seconds
Madeline: 10 seconds
Badeline: 10.1 seconds

Pada direktory : `https://mount-tunnel.web.actf.co/play`
Kalo kita melihat source code :
```js
<form action="/submit" method="POST">
  <input type="text" style="display: none;" value="1682888046.9222546" name="start" />
  <input type="submit" value="Press when done!" />
</form>
```
Perlu diingat form action merujuk `/submit` dengan method : `POST`.

So....?????

How To Solve?
```js
import requests

r = requests.post("https://chall/submit", data={"start":"1685888046.9222546"}) #change random value : 1685888046.9222546
print(r.text)
```

flag : `you win the flag: actf{wait_until_farewell_speedrun}`
