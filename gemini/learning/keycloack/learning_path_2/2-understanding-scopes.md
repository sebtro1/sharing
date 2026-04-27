# Understanding Scopes

You are exactly right! The frontend would use the **OpenID Connect (OIDC)** protocol, and specifically, it would look inside the **ID Token** (which is formatted as a JWT) to find the user's email address. 

But this raises an interesting question: How did Keycloak know to put the user's email address into that ID Token in the first place? And how does an Access Token know exactly what permissions to hold?

The answer is **Scopes**.

### What is a Scope?
In OAuth 2.0 and OIDC, a **Scope** is a way for the Client (your application) to request a specific slice of data or a specific permission from the Authorization Server (Keycloak). 

When your frontend redirects the user to Keycloak to log in, the URL actually contains a parameter like this: `scope=openid profile email`.

*   `openid`: This is the magic word that tells Keycloak, "I want to use OpenID Connect, and I need an ID Token to authenticate the user."
*   `profile`: This tells Keycloak, "Please put the user's basic profile information (like their name) into the token."
*   `email`: This tells Keycloak, "Please put the user's email address into the token."

### Scopes vs Roles
It is very common to confuse Scopes and Roles, especially when configuring Keycloak for a Spring Backend. 

*   **Roles are about *who* the user is.** (e.g., "This user is an Admin"). They are assigned to the user permanently in the Keycloak Admin Console.
*   **Scopes are about *what* the application is asking for during this specific session.** (e.g., "This application wants to read the user's email address"). 

When you configure your Keycloak Client, you can define custom Scopes. For example, if you had a billing API, you might create a scope called `billing:read`. Then, the frontend must explicitly request the `billing:read` scope when the user logs in, and the Access Token will reflect that permission.

### Client Scopes in Keycloak
Inside the Keycloak Admin Console, there is an entire menu tab dedicated to **Client Scopes**. This is where you act as the architect, mapping specific user data (like a database field) into the JWT payload, but *only* if the application requested the corresponding Scope.
