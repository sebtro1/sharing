# The Big Shift: Basic Auth vs. Token Auth

Right now, with **Basic Auth**, your flow probably looks like this:

1.  A user (or an application) sends a request to your Spring Backend.
2.  Attached to that request is a base64-encoded string containing a **username and password**.
3.  Your Spring Backend looks at the username and password, checks if they are valid, and decides whether to allow the request.

With **Keycloak (Token-Based Auth via OAuth 2.0/OpenID Connect)**, your flow will change to this:

1.  The user goes to **Keycloak** first and logs in with their username and password.
2.  Keycloak verifies them and hands them a temporary **Access Token** (specifically, a JSON Web Token, or JWT).
3.  The user sends their request to your **Spring Backend**, attaching this *Access Token* instead of their password.
4.  Your Spring Backend looks at the token, verifies it was signed by Keycloak, and allows the request.

> **Why is this better?** Your Spring Backend no longer has to handle or even know the user's password! It just trusts the tokens that Keycloak issues.
