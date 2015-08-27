# HTTP Auth Interceptor Module for Angular 1.4+

This will :
 - intercept all the requests done in the app before your user logs in
 - broadcasts a message upon reception of a 401 response
 - after login is confirmed, will receive a broadcast and retry the previously pending requests
 - allows you to configure $http for the next requests in your app (for example, you can add a header, like a token for your API)

#### Usage


Register this module in your app :

```javascript
var app = angular.module('app', [ 'HttpAuthInterceptor' ]);
```

And then in your user service :

```javascript
app.service('UserService', [ '$http', '$cookies', 'AuthInterceptorService', 'ConstantsService',
	function($rootScope, $http, $cookies, AuthInterceptorService, ConstantsService) {

	var service = {
					
        (...)

        /*
        * Call this function when the user enters his login and password
        */
	checkLoginAndPassword : function(login, password, rememberMe) {
		$http.post('/user/login', {
			'login' : login,
			'password' : password,
			'rememberMe' : rememberMe
		}).success(function(data, status, headers, config) {
			if (data.success) {
				var xAuthorization = data.xAuthorization;
				AuthInterceptorService.loginConfirmed('success', function(config) {
					config.headers["X-Authorization"] = xAuthorization;
					return config;
				});
			}
		}
	}
```
