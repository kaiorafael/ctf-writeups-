# PicoCTF

## Web Exploitation

##### Problem

###### It is my Birthday
> Description
> I sent out 2 invitations to all of my friends for my birthday! I'll know if they get stolen because the two invites look similar, and they even have the same md5 hash, but they are slightly different! You wouldn't believe how long it took me to find a collision. Anyway, see if you're invited by submitting 2 PDFs to my website. http://mercury.picoctf.net:48746/

Solution:

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

```php
// FLAG: picoCTF{c0ngr4ts_u_r_1nv1t3d_aebcbf39}
```
