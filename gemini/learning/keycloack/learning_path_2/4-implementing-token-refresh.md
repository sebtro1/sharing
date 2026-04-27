# Implementing Token Refresh in the Browser

That is an excellent practical question. The short answer is: **You do not need to program the complex logic from scratch, but it does not happen purely by magic either.** You have to use a library to handle it.

The browser itself (Chrome, Firefox, etc.) does not know what a Keycloak Refresh Token is. If you just wrote vanilla JavaScript without any libraries, you *would* have to program the logic to check the expiration time and make the API call manually.

However, no one does that. Instead, you use an **OIDC Client Library**.

### The Keycloak JavaScript Adapter
Keycloak provides official libraries for exactly this reason. The most common one for the frontend is `keycloak-js`.

When you include `keycloak-js` in your React, Angular, Vue, or Vanilla JS project, you initialize it once. After that, it handles the heavy lifting.

Here is what the code typically looks like using the official adapter:

```javascript
// 1. Initialize Keycloak
const keycloak = new Keycloak({
    url: 'http://localhost:8080',
    realm: 'my-realm',
    clientId: 'my-frontend-client'
});

keycloak.init({ onLoad: 'login-required' }).then(function(authenticated) {
    if (authenticated) {
        console.log("User is logged in!");
    }
});

// 2. The "Magic" Refresh Logic
// Before you make an API call to your Spring Backend, you call updateToken()
keycloak.updateToken(30).then(function(refreshed) {
    if (refreshed) {
        console.log('Token was successfully refreshed in the background');
    } else {
        console.log('Token is still valid, no refresh needed');
    }
    
    // 3. Make the actual API call to Spring
    fetch('http://localhost:8081/api/data', {
        headers: {
            'Authorization': 'Bearer ' + keycloak.token
        }
    });
}).catch(function() {
    console.log('Failed to refresh token (e.g., refresh token expired or user was banned).');
    keycloak.login(); // Force them to login again
});
```

### What `updateToken(30)` actually does:
The `30` means "Is the token going to expire in the next 30 seconds?". 
*   If the token is valid for another 5 minutes, `updateToken` does absolutely nothing and instantly returns.
*   If the token expires in 10 seconds, `updateToken` pauses your code, makes the background HTTP request to Keycloak using the Refresh Token, gets the *new* token, updates the internal variables, and then continues.

### Modern Alternatives
If you are using a modern frontend framework, you might use wrapper libraries instead of `keycloak-js` directly:
*   `react-oidc-context` (for React)
*   `angular-oauth2-oidc` (for Angular)
*   `next-auth` (for Next.js)

These libraries do the exact same thing under the hood. They usually provide an "Interceptor" that automatically intercepts every HTTP request your frontend makes, checks if the token needs refreshing, refreshes it if necessary, and then automatically attaches the token to the header before sending the request to Spring. You don't even have to call `updateToken` yourself!
