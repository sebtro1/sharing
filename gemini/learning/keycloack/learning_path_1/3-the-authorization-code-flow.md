# The Authorization Code Flow

Spot on! You nailed it. The frontend should **never** see the user's password. It redirects the user to Keycloak, Keycloak handles the login securely, and then redirects the user back to the frontend with a token.

This exact process you described is the industry standard, and it is called the **Authorization Code Flow** (often used with an extension called PKCE for public clients like single-page apps).

Here is the step-by-step breakdown of how it works in your scenario:

1.  **The Redirect:** The user clicks "Login" in your frontend. The frontend redirects the user's browser to Keycloak's login page.
2.  **The Authentication:** The user enters their username and password directly into Keycloak.
3.  **The Code:** Keycloak verifies the credentials and redirects the user's browser back to your frontend, attaching a temporary, one-time-use "Authorization Code" in the URL.
4.  **The Exchange:** The frontend takes that Code and makes a secure, background API call to Keycloak to exchange it for an **Access Token** (the JWT).
5.  **The API Call:** The frontend can now attach that Access Token to the HTTP `Authorization` header whenever it calls your Spring Backend.

### Why not just give the token immediately in step 3?
Passing the actual Access Token in the URL during a redirect is considered insecure (it could be saved in browser history, server logs, or intercepted). The "Code" is much safer because it can only be exchanged for a token by the specific application (Client) that requested it.
