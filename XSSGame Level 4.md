# **XSSGame Level 4**

![](https://i.imgur.com/QODKpi6.png)
```
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="/static/css/level_style.css" />
    <script src="/static/js/js_frame.js"></script>
  </head>
  <body style="background-color: white;">
    <center>
      Welcome! Today we are announcing the much anticipated<br>
      <img src="/static/img/googlereader.png" /><br>
      <a href="signup?next=confirm">Sign up</a> for an exclusive Beta.
    </center>
  </body>
</html>
```

![](https://i.imgur.com/gC0GGeu.png)
```
<!DOCTYPE html>
<html>
  <head>
    <link rel="stylesheet" href="/static/css/level_style.css" />
    <script src="/static/js/js_frame.js"></script>
  </head>
  <body>
    <center>
      <img src="/static/img/googlereader-logo.png" /><br><br>
      <!-- We're ignoring the email, but the poor user will never know! -->
      Enter email: <input id="reader-email" name="email" value="">
      <br><br>
      <a href="confirm?next=welcome">Next >></a>
    </center>
  </body>
</html>
```
`<!-- We're ignoring the email, but the poor user will never know! -->`

Well, Okay. ˊ_>ˋ

Here's the key point:

`<a href="confirm?next=welcome">Next >></a>`

![](https://i.imgur.com/E9L3MUf.png)

```
<!DOCTYPE html>
<html>
  <head>
    <script src="/static/js/js_frame.js"></script>
  </head>
  <body style="background-color: white;">
    <center>
      <img src="/static/img/googlereader-logo.png" /><br><br>
      Thanks for signing up, you will be redirected soon...
      <script>
        setTimeout(function() { window.location = 'welcome'; }, 1000);
      </script>
      
    </center>
  </body>
</html>
```

`$_GET['welcome']` will be `walcome` in `setTimeout(function() { window.location = 'welcome'; }, 1000);`

So, `<a href="confirm?next=welcome">Next >></a>` is the inject point.

Let's inject `confirm?next=javascript:alert()` to it.
![](https://i.imgur.com/5e72BZi.png)
