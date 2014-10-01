# angular-jwt

This library will help you work with JWTs. It's comprehended of 2 basic services

## Installing it

You have several options:

````bash
bower install angular-jwt
````

````bash
npm install angular-jwt
````

````html
<script type="text/javascript" src="https://rawgit.com/auth0/angular-jwt/master/dist/angular-jwt.js"></script>
````

## jwtHelper

jwtHelper will take care of helping you decode the token and check its expiration date.

### Decoding the token

````js
function Controller(jwtHelper) {
  var expToken = 'eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJodHRwczovL3NhbXBsZXMuYXV0aDAuY29tLyIsInN1YiI6ImZhY2Vib29rfDEwMTU0Mjg3MDI3NTEwMzAyIiwiYXVkIjoiQlVJSlNXOXg2MHNJSEJ3OEtkOUVtQ2JqOGVESUZ4REMiLCJleHAiOjE0MTIyMzQ3MzAsImlhdCI6MTQxMjE5ODczMH0.7M5sAV50fF1-_h9qVbdSgqAnXVF7mz3I6RjS6JiH0H8';  

  var tokenPayload = jwtHelper.decodeToken(expToken);
}
````
### Getting the token expiration date

````js
function Controller(jwtHelper) {
  var date = jwtHelper.getTokenExpirationDate(expToken);
}
````

### Checking if token is expired

````js
function Controller(jwtHelper) {
  var bool = jwtHelper.isTokenExpired(expToken);
}
````

### More examples

You can see some more examples of how this works in [the tests](https://github.com/auth0/angular-jwt/blob/master/test/unit/angularJwt/services/jwtSpec.js)

## jwtInterceptor

JWT interceptor will take care of sending the JWT in every request.

### Basic usage

````js
function Config($httpProvider, jwtInterceptorProvider) {
  jwtInterceptorProvider.tokenGetter = function(localStorage) {
    return localStorage.getItem('id_token');
  }
  $httpProvider.interceptors.push('jwtInterceptor');
}

function Controller($http) {
  // If localStorage contains the id_token it will be sent in the request
  // Authorization: Bearer [yourToken] will be sent
  $http({
    url: '/hola',
    method: 'GET'
  });
}
````

### Using promises on the `tokenGetter`: Refresh Token example

As sometimes we need to get first the `id_token` in order to send it, we can return a promise in the `tokenGetter`. Let's see for example how we'd use a `refresh_token`

````js
function Config($httpProvider, jwtInterceptorProvider) {
  jwtInterceptorProvider.tokenGetter = function(localStorage, jwtHelper) {
    var idToken = localStorage.getItem('id_token');
    var refreshToken = localStorage.getItem('refresh_token');
    if (jwtHelper.isTokenExpired(idToken)) {
      // This is a promise of a JWT id_token
      return auth.refreshToken(refreshToken).then(function(id_token) {
        localStorage.setItem('id_token', id_token);
        return id_token;
      });
    } else {
      return idToken;
    }
  }
  $httpProvider.interceptors.push('jwtInterceptor');
}

function Controller($http) {
  // Authorization: Bearer [yourToken] will be sent. 
  // That token might be a new one which was got from the refresh token
  $http({
    url: '/hola',
    method: 'GET'
  });
}
````

### More examples

You can see some more examples of how this works in [the tests](https://github.com/auth0/angular-jwt/blob/master/test/unit/angularJwt/services/interceptorSpec.js)

## Contributing

Just clone the repo, run `npm install`, `bower install` and then `gulp` to work :).

## License

MIT
