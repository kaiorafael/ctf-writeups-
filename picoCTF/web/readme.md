# PicoCTF

## Web Exploitation

##### Problem

###### dont-use-client-side
> Description
> Can you break into this super secure portal? https://jupiter.challenges.picoctf.org/problem/29835/ (link) or http://jupiter.challenges.picoctf.org:29835

**Solution**:

To capture the flag one needs to `Enter valid credentials to proceed`. There is a JavaScript that checks for that.

```js
  function verify() {
    checkpass = document.getElementById("pass").value;
    split = 4;
    if (checkpass.substring(0, split) == 'pico') {
      if (checkpass.substring(split*6, split*7) == '723c') {
        if (checkpass.substring(split, split*2) == 'CTF{') {
         if (checkpass.substring(split*4, split*5) == 'ts_p') {
          if (checkpass.substring(split*3, split*4) == 'lien') {
            if (checkpass.substring(split*5, split*6) == 'lz_7') {
              if (checkpass.substring(split*2, split*3) == 'no_c') {
                if (checkpass.substring(split*7, split*8) == 'e}') {
                  alert("Password Verified")
                  }
                }
              }
            }
          }
        }
      }
    }
```

You just have to follow the white rabbit. First part the string is  `substring(0, split) == 'pico')`. Second is `substring(split, split*2) == 'CTF{')`. Third is `substring(split*2, split*3) == 'no_c')`. Forth part is `substring(split*3, split*4) == 'lien')`, so on so forth, and final part denotes `substring(split*7, split*8) == 'e}')`.


**FLAG**

```
picoCTF{no_clients_plz_7723ce}
```


###### It is my Birthday
> Description
> I sent out 2 invitations to all of my friends for my birthday! I'll know if they get stolen because the two invites look similar, and they even have the same md5 hash, but they are slightly different! You wouldn't believe how long it took me to find a collision. Anyway, see if you're invited by submitting 2 PDFs to my website. http://mercury.picoctf.net:48746/

**Solution**:

This web application is vulrnable to the idea of MD5 collision. Basically, to solve this challenge one needs to send _two_ files that generates the same hash output.

I dowloaded the following files from: https://www.mathstat.dal.ca/~selinger/md5collision/

```sh
# first file
curl -O https://www.mathstat.dal.ca/~selinger/md5collision/erase
cp erase 1.pdf

# second file
curl -O https://www.mathstat.dal.ca/~selinger/md5collision/hello
cp hello 2.pdf
```

Now, upload `1.pdf` and `2.pdf`. You will receive the flag:

**FLAG**
```php
// FLAG: picoCTF{c0ngr4ts_u_r_1nv1t3d_aebcbf39}
```
