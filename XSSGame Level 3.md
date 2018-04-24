# **XSSGame Level 3**

![](https://i.imgur.com/pYtXchz.png)

Photo Switcher, just like xss-game level 3.

We assume that HTML is written as `<img src='x'/>` when input is `frame#X`.

We give in `' onerror='alert(1)'`, so how it went wrong, or right? Just say that it doesn't return alert box.

Let's find out the problem.

![](https://i.imgur.com/15Upti3.png)

Symbol in url get encoded, fortunately, it only single quote get rid of that?

`'onerror='alert()'`
![](https://i.imgur.com/2ChbtXD.png)
![](https://i.imgur.com/cH1BB5O.png)

It seens double quotes has special treating?

![](https://i.imgur.com/ZP5gn0F.png)
