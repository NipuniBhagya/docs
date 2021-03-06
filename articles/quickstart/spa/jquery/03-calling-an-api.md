---
title: Calling an API
description: This tutorial demonstrates how to make API calls for protected resources on your server
budicon: 546
---

<%= include('../../../_includes/_package', {
  org: 'auth0-samples',
  repo: 'auth0-jquery-samples',
  path: '03-Calling-an-API',
  requirements: [
    'jQuery 3.2.1'
  ]
}) %>

<%= include('../_includes/_calling_api_preamble') %>

<%= include('../_includes/_calling_api_create_api') %>

<%= include('../_includes/_calling_api_create_scope') %>

## Set the Audience and Scope in `auth0.WebAuth`

Pass the API identifier for your newly created API as the `audience` value in your `auth0.WebAuth` instance. Additionally, pass any of your newly created scopes to the `scope` key.

```js
// app.js

var webAuth = new auth0.WebAuth({
  // ...
  audience: '${apiIdentifier}',
  scope: 'openid profile read:messages'
});
```

::: note
**Checkpoint:** At this point you should try logging into your application again to take note of how the `access_token` differs from before. Instead of being an opaque token, it is now a JSON Web Token which has a payload that contains your API identifier as an `audience` and any `scope`s you've requested.
:::

::: note
By default, any user on any client can ask for any scope defined in the scopes configuration area. You can implement access policies to limit this behaviour via [Rules](/rules).
:::

## Configure a Custom XHR Request

<%= include('../_includes/_calling_api_access_token') %>

Attaching the user's `access_token` as an `Authorization` header to HTTP calls can be done on a one-off basis by adding the header as an option to your requests. However, it is recommended that you implement a custom function which does this automatically.

Create a new function called `callAPI` which wraps a jQuery `$.ajax` request. If the request should be secured and if there is an `access_token` in local storage, attach it as the `Authorization` header.

```js
// app.js

var apiUrl = 'http://localhost:3001/api';

// ...
function callAPI(endpoint, secured) {
  var url = apiUrl + endpoint;
  var accessToken = localStorage.getItem('access_token');

  var headers;
  if (secured && accessToken) {
    headers = { Authorization: 'Bearer ' + accessToken };
  }

  $.ajax({
    url: url,
    headers: headers
  })
    .done(function(result) {
      $('#ping-view h2').text(result.message);
    })
    .fail(function(err) {
      $('#ping-view h2').text('Request failed: ' + err.statusText);
    });
}
```

<%= include('../_includes/_calling_api_protect_resources') %>
