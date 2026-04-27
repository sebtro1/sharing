# Refresh Tokens

You are completely correct on the concept: the frontend is smart enough to proactively ask Keycloak for a new token before the old one expires. 

But *how* does it prove it's allowed to get a new one without asking the user for their password again? It uses a **Refresh Token**.

### The Three Tokens
When the user successfully logs in, we mentioned Keycloak returns multiple tokens. In reality, a standard OpenID Connect flow returns *three* tokens:

1.  **ID Token:** Tells the frontend who the user is. (Short-lived)
2.  **Access Token:** Sent to the Spring Backend to prove permission. (Short-lived, e.g., 5 minutes).
3.  **Refresh Token:** A special, highly guarded token sent only to the frontend. (Long-lived, e.g., 30 days).

### The Refresh Flow

Here is how the application keeps the user logged in seamlessly:

1.  The frontend monitors the lifespan of the Access Token.
2.  When the Access Token is about to expire (say, 30 seconds left), the frontend makes a silent, background API request directly to Keycloak.
3.  In this request, the frontend sends the **Refresh Token**. 
4.  Keycloak receives the Refresh Token, validates it, and checks if the user's account is still active (e.g., they haven't been banned or deleted by an admin in the last 5 minutes).
5.  If everything is good, Keycloak sends back a brand new **Access Token** and a new **Refresh Token**.
6.  The user continues using the application without ever knowing this happened!

### Why is this secure?
If an attacker steals an **Access Token** from a network request, they can only use it for a few minutes before it expires. They cannot use an Access Token to get a new one.

To get a new token, the attacker would have to steal the **Refresh Token**. However, Refresh Tokens are never sent to your Spring Backend or attached to standard API requests. They are kept securely locked away in the frontend (often in an `HttpOnly` secure cookie or secure memory) and only ever sent directly to Keycloak's secure token endpoint. 

Furthermore, if an administrator detects suspicious activity, they can go into the Keycloak Admin Console and instantly "Revoke" a user's session. This immediately invalidates the Refresh Token, meaning the next time the frontend tries to refresh silently, it will fail, and the user will be forced to log in again.
