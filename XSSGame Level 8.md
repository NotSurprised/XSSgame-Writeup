# **XSSGame Level 8**

![](https://i.imgur.com/2oAI7Qx.png)

![](https://i.imgur.com/ywew8xa.png)

Url to set the name :`http://www.xssgame.com/f/d9u16LTxchEi/set?name=name&value=pass&redirect=index`.

`name` is a default value, we will trace deeper later. `redirect` should be the page to redirect.

![](https://i.imgur.com/jCqCEJM.png)

Let's try to type something to make a trransaction.

![](https://i.imgur.com/TiwPGme.png)

The amount will be print to the page after we request a transaction.

Maybe we can try some XSS payload?

![](https://i.imgur.com/7CPiPPx.png)
![](https://i.imgur.com/NlerWdv.png)

Failed, due to the `csrf_token`, this payload cannot used by anyone else.

In `level8.js` we see this:

```
/**
 * Read cookie.
 * @param {string} name - Name of the cookie
 * @returns {string} Cookie value
 */
function readCookie(name) {
    var match = RegExp('(?:^|;)\\s*' + name + '=([^;]*)').exec(document.cookie);
    return match && match[1];
}

var username = readCookie('name');
if (username) {
    document.write('<h1>Welcome ' + username + '!</h1>');
}

document.addEventListener("DOMContentLoaded", function(event) {
    csrf_token.value = readCookie('csrf_token');
});
```

In orginal default, we see that csrf_token has a default value:

![](https://i.imgur.com/H2mdbTt.png)

But, from `level8.js`, it seems we can set `csrf_token` by set a value we want to `name`, like `name=pass` then `csrf_token`'s value will also be `pass`, as following:

![](https://i.imgur.com/fsVV61G.png)

After we set `csrf_token` already by **set name page**, let's inject payload again.

`transfer%3Fname%3Dhack%26amount%3D%253Cscript%253Ealert()%253C%2Fscript%253E%26csrf_token%3Dpass`

![](https://i.imgur.com/YMldTdN.png)

Well, it seems `csrf_token` get modified or what, eventually not matched.

Maybe we should use `redirect` to make this payload success in one step.

`set?name=csrf_token&value=pass&redirect=transfer%3Fname%3Dhack%26amount%3D%253Cscript%253Ealert()%253C%2Fscript%253E%26csrf_token%3Dpass`

![](https://i.imgur.com/KWgnvAF.png)

![](https://i.imgur.com/xAt6kDt.png)

Fin.